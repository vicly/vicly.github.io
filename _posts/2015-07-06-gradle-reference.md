---
layout:     post
title:      Gradle Reference
date:       2015-07-06 16:53:19
summary:    Some Gradle related code snappet
categories: dev
tags:
- gradle
- ref
---


## Task vs. method
{% highlight groovy %}
// this is task
task myTask << {
    fileList('aDir').each {File file -> ...} // call method
}
// this is method
File[] fileList(String dir) {
    file(dir).listFiles( { file -> file.isFile() } as FileFilter ).sort() 
}
{% endhighlight %}


## Incremental build
{% highlight groovy %}
task transform {
    ext.srcFile = file('mountains.xml')
    ext.destDir = new File(buildDir, 'generated')
    //--------------------------------
    inputs.file srcFile
    outputs.dir destDir
    //--------------------------------
    doLast {
        println "Transforming source file."
        destDir.mkdirs()
        def mountains = new XmlParser().parse(srcFile)
        mountains.mountain.each { mountain ->
            def name = mountain.name[0].text()
            def height = mountain.height[0].text()
            def destFile = new File(destDir, "${name}.txt")
            destFile.text = "$name -> ${height}\n"
        }
    }
}
{% endhighlight %}

http://www.gradle.org/docs/current/javadoc/org/gradle/api/tasks/TaskInputs.html
http://www.gradle.org/docs/current/javadoc/org/gradle/api/tasks/TaskOutputs.html

**How does it work?**

Before a task is executed for the first time, Gradle takes a snapshot of the inputs. This snapshot contains the set of input files and a hash of the contents of each file. Gradle then executes the task. If the task completes successfully,Gradle takes a snapshot of the outputs. This snapshot contains the set of output files and a hash of the contents of each file. Gradle takes note of any files created, changed or deleted in the output directories of the task. Gradle persists both snapshots for next time the task is executed. Each time after that, before the task is executed, Gradle takes a new snapshot of the inputs and outputs. If the new snapshots are the same as the previous snapshots, Gradle assumes that the outputs are up to date and skips the task. If they are not the same, Gradle executes the task. Gradle persists both snapshots for next time the task is executed.


## Disable test incremental build
{% highlight groovy %}
test{
    outputs.upToDateWhen { false }
}
{% endhighlight %}


## Convenient test execution with camelCase support
{% highlight groovy %}
tasks.addRule("Pattern: test<TestClass>: Runs a single test or a pattern of tests") { String taskName ->
    final String prefix = "test"
    if (taskName.startsWith(prefix) && taskName.length() > prefix.length()) {
        logger.info("SingleTestExecution: $taskName matches pattern")
  
        // create a dummy task for the task name specified on the command line
        Task dummyTask = task(taskName)
  
        // remove prefix from the specified task name
        def className = taskName - prefix
  
        // define an include that supports the specified naming pattern, incl. camel-case notation
        def camelCaseTaskName = className[0] + className.substring(1).collect() { it == it.toUpperCase() ? "*$it" : it }.join("")
        def testIncludes = WrapUtil.toSet(String.format("**/%s*Test.class", camelCaseTaskName))
        logger.info("SingleTestExecution: include pattern <$testIncludes>")
  
        // make all Test tasks a dependency of the dummy task and reset the includes
        tasks.withType(Test) { Task testTask ->
            logger.info("SingleTestExecution: apply include pattern to Test task <$testTask.name>")
            testTask.includes = testIncludes
            dummyTask.dependsOn testTask
        }
    }
}
 
 
// before
$ gradle test -Dtest.single=UserServiceProxyTest
 
// after
$ gradle testUserServiceProxy
{% endhighlight %}


## Invoke task in another gradle script
{% highlight groovy %}
// build.gradle
task build(type: GradleBuild) {
    buildFile = 'other.gradle'
    tasks = ['myTask1', 'myTask2']
}
 
// other.gradle
task myTask1 << { ... }
task myTask2 << { ... }
{% endhighlight %}



## Resolve files, dependencies
{% highlight groovy %}
// To get the resolved files, iterate over the configuration or call its getFiles() method. 
 
// To get the resolved artifacts (logical representation of the files), use configurations.runtime.resolvedConfiguration.resolvedArtifacts. 
 
// To get the resolved dependencies (the kind of information that you see in a "dependencies" block), use configurations.runtime.resolvedConfiguration.firstLevelModuleDependencies and navigate down the dependency tree with getChildren().
 
 
 
task myDependencies << {
    projectList = rootProject.subprojects.collect{it.name}
    configurations.runtime.resolvedConfiguration.firstLevelModuleDependencies.each{directDep ->
        addChildsToList(directDep)
    }
}
 
dependencyList = []
def addChildsToList (def pResolvedDependency) {
    pResolvedDependency.children.each{
        addChildsToList(it)
    }
    if(!dependencyList.contains(pResolvedDependency))
        dependencyList << pResolvedDependency
}
{% endhighlight %}



## Copy dependency jars
{% highlight groovy %}
allprojects {
    apply plugin: 'java'
}
 
repositories { mavenCentral() }
 
configurations {
    findbugs
}
 
dependencies {
    // copy findbugs and declared transitive jars
    findbugs module('com.google.code.findbugs:findbugs:2.0.1') {
        // just copy jaxen, don't copy its transitive dependencies
        dependency("jaxen:jaxen:1.1.1") {
            transitive = false
        }
        // more findbugs's transitive dependenct jar
        dependencies 'com.google.code.findbugs:bcel:2.0.1', 'asm:asm:3.3'
    }
}
task copyFindBugs(type: Copy) {
    from configurations.findbugs
    into "lib"
}
{% endhighlight %}



## Copy and rename
{% highlight groovy %}
copy {
    from "${buildDir}/tmp/warApplication/WEB-INF/classes"
    into "${buildDir}/tmp/warApplication/WEB-INF/classes"
    include "${thidsProjectName}.jar.properties"
    rename (/([a-z]+)\.jar\.([a-z]+)/, 'newname.jar.$2')
}
{% endhighlight %}


## Add integTest sourceSet
{% highlight groovy %}
apply plugin: "java"
 
sourceSets {
    // Note that just declaring this sourceset creates two configurations.
    intTest {
        java {
            compileClasspath += main.output
            runtimeClasspath += main.output
        }
    }
}
 
configurations {
    intTestCompile.extendsFrom testCompile
    intTestRuntime.extendsFrom testRuntime
}
 
task intTest(type:Test){
    description = "Run integration tests (located in src/intTest/...)."
    testClassesDir = project.sourceSets.intTest.output.classesDir
    classpath = project.sourceSets.intTest.runtimeClasspath
}
{% endhighlight %}


## Dynamically create task
{% highlight groovy %}
["env1", "env2"].each { environment ->
  def enviromentCapitalized = environment.capitalize()
  task("conf${enviromentCapitalized}", dependsOn: "init${enviromentCapitalized}Config", type: Tar) {
    // xxx
  }
  task("init${enviromentCapitalized}Config", type: Copy) {
    // xxx
  }
}
{% endhighlight %}


## Calculate resolved dependencies
{% highlight groovy %}
def finalJarList = []
project(':myProject').configurations.runtime.resolvedConfiguration.firstLevelModuleDependencies.each { directDep ->
    addChildsToList(finalJarList, directDep)
}
 
 
def addChildsToList(def container, def pResolvedDependency) {
    if (!container.contains(pResolvedDependency.name)) {
        container << pResolvedDependency.name
    }
    pResolvedDependency.children.each {
        addChildsToList(container, it)
    }
}
{% endhighlight %}


## Wrap ANT task
{% highlight groovy %}
configurations {
    ftpAntTask
}
 
dependencies {
    ftpAntTask("org.apache.ant:ant-commons-net:1.8.4") {
        module("commons-net:commons-net:1.4.1") {
            dependencies "oro:oro:2.0.8:jar"
        }
    }
}
 
task ftp << {
    ant {
        taskdef(name: 'ftp',
                classname: 'org.apache.tools.ant.taskdefs.optional.net.FTP',
                classpath: configurations.ftpAntTask.asPath)
        ftp(server: "ftp.apache.org", userid: "anonymous", password: "me@myorg.com") {
            fileset(dir: "htdocs/manual")
        }
    }
}
{% endhighlight %}


## Skip a task
{% highlight groovy %}
task hello << {
    println 'hello world'
}
hello.onlyIf { !project.hasProperty('skipHello') }
{% endhighlight %}


## Overwrite a task
{% highlight groovy %}
task copy(type: Copy)
 
task copy(overwrite: true) << {
    println('I am the new one.')
}
{% endhighlight %}


## Task dependency
{% highlight groovy %}
taskX.dependsOn taskY    // by task, taskY must be defined before taskX
taskX.dependsOn 'taskY'  // by name, no above constraint

taskX.dependsOn {
    tasks.findAll { task -> task.name.startsWith('lib') }
}
{% endhighlight %}




## gradle.properties notes

* automatically be loaded
* each project can has this file
* the property name can directly be used as variable in gradle script
* using prefix `systemProp` to define system property in `gradle.properties` file, e.g. `systemProp.mySysProp=sysPropValue`
* `-PmyProp=newValue` CLI parameter
 * define a normal property (NOT system property)
 * will overwrite `myProp` defined in `gradle.properties`
 * will **_NOT_** overwrite the system property with the same name
* Normal property and System property are saved in two different buckets, system property can be overwritten by System.setProperty(NAME, VALUE)


## Misc
* on Windows, when running gradle, `$rootDir` will be locked, Linux has not such problem, so don't WRITE `$rootDir`, e.g. create file, use a sub folder
* Get project's artifacts: `project.configurations.archives.artifacts*.file`
* Resource output: `sourceSets.main.output.resourcesDir`
* Maven classifier: `compile "org.gradle.test.classifiers:service:1.0:jdk15@jar"`
* `<<` is simply an alias for `doLast`
* Set default tasks: `defaultTasks: 'clean', 'run'`
* If a task is not defined in subproject, it will try to use the one in parent (if defined)

