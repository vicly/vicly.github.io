---
layout:     post
title:      Handy Development Reference
date:       2015-07-06 14:37:19
summary:    Daily reference
categories: dev
tags:
- git
- ref
---

## Git

### Ref
{% highlight bash %}
#-------------------------------------------
# Merge and Rebase
#-------------------------------------------
# Merge: merge into current branch; current branch is "master"
# Rabase: rebase onto given branch; current branch is "feature-test"
 
# Rebase
git checkout -b feature-test
# edit and commit into feature-test
# some change committed on master
git rebase master
# might need solve conflict
 
# Merge
git checkout master
git merge feature-test
git push
# might need solve conflict
git branch -d feature-test       # remove feature branch

#-------------------------------------------
# Overwrite a branch with another one
#   better_branch => master
#-------------------------------------------
git checkout better_branch
git merge --strategy=ours master    # keep the content of this branch, but record a merge
git checkout master
git merge better_branch             # fast-forward master up to the merge

#-------------------------------------------
# branch, tag
#-------------------------------------------
 
# List branch
git branch                                  # list available branches
git branch -a                               # list all (local+remote)
git branch -r                               # list remote only
 
# Create branch
git checkout <branch|tag>                   # switch branch
git checkout -b <branch>                    # create and checkout
git checkout -b <new> [existing]            # create branch base on given branch/tag/commit
git branch <branch>                         # create
 
# Delete
git branch -d <branch>                      # delete local
git push origin :<branch>                   # delete remote
git push origin --delete <branch>           # delete remote
git branch -D <branch>                      # force delete

# Rename
git branch -m <new_name>                    # rename current branch
git branch -m <old_name> <new_name>         # rename
 
#-------------------------------------------
# tag
#-------------------------------------------
git tag                                     # list
git tag 1.7.1                               # create lightweight tags
git tag 1.5.1 -m 'Release 1.5.1'            # create tag base on current branch's head version
git tag 1.5.1 -m 'version 1.5' <commitId>   # create tag base on commit id
git checkout <tag>                          # checkout tag
git push origin <tag>                       # push a tag
git push origin tag <tag>
git tag -d 1.5.1                            # delete tag
 
 
git tag 1.0                                 # create
git push origin 1.0                         # push to remote
git tag -d 1.0                              # delete local tag
git push --delete origin 1.0                # delete remote tag
 
 
#-------------------------------------------
# config
#-------------------------------------------
# define the author
git config user.name <name>                              # current repo only
git config --global user.name <name>                     # all repo
git config --global user.email <email>
 
git config --global alias.<alias-name> <git-command>     # command shortcut
git config --system core.editor <editor command>         # define the editor
git config --global --edit                               # open global configuration file
 
git config --global branch.autosetuprebase always        # for each 'pull', rebase first

#-------------------------------------------
# stash
#-------------------------------------------
# did some change, not commit yet, and wanna switch to another job, e.g. pull
 
git stash           # hide
git pull            # do new job
git stash apply     # get back
 
 
 
git stash list               # list all
git stash apply stash@{1}    # get back specific change
git stash pop                # get back the latest change
git stash drop <id>          # delete ONE
git stash clear              # delete ALL

 
#-------------------------------------------
# clean
#-------------------------------------------
git clean -df                                 # rm untracked files and directories, but not files specified by .gitignore
git clean -dfxn                               # dry run: rm all untracked files+dirs, include files specified by .gitignore
git clean -dfx                                # rm all untracked files+dirs
 
#-------------------------------------------
# grep
#-------------------------------------------
git grep Xyz                           # search current branch
git grep Xyz <branch>                  # search specified branch
git grep -c Xyz                        # how many matched
git grep -n Xyz                        # show line number
git grep -e term1 --and -e term2       # AND
git grep --all-match -e term1 -e term2 # OR
git grep -e term1 --and \(-e term2 -e term3\) # term1 and (term2 or term3)
 
#-------------------------------------------
# handy
#-------------------------------------------
# Print HEAD version
cat .git/HEAD
cat .git/refs/heads/master

git add -i                     # interactively add

git log --oneline              # single line
git log --stat                 # file list and +/- count
git log -p                     # file content
git log --author="Vic"         # find by author
git log --grep="<pattern>"     # grep content
git log <file>                 # include specific file
git log --graph --decorate --oneline # branch relationship
git push origin master         # specify remote branch
 
git reset                     # reset staging area, leave working dir unchanged
git reset --hard              # reset staging area and working dir
 
git show                      # HEAD
git show HEAD~                # previous version
 
git rev-parse HEAD            # Current Version
 
gitk <branch1> <branch2>      # Show log diff between two branches
 
git diff <newVer> <oldVer>    # Compare two commits
 
# set tracking info
git branch --set-upstream-to=origin/<parent-branch> <your-branch>
 
# compare 2 branches
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative master..feature-x
 
 
# drop uncommit change
git checkout prj1/main.py

#-------------------------------------------
# clean copy
#-------------------------------------------
git fetch                               # fetch from remote
git reset --hard origin/<branch>        # align with remote
git clean -dfx                          # clean all untracked files

#-------------------------------------------
# Remove a commit
#-------------------------------------------
# Scenario 1: local commits not pushed yet
git reset --hard origin/<branch>
 
# Scenario 2: local commits pushed, remove latest 3 commits
git reset --hard HEAD~3
git push origin HEAD --force

#-------------------------------------------
# Split a commit into multiple ones
#-------------------------------------------
git rebase -i 
# mark 'edit'
# ctrl+X
git reset HEAD^
git add file1
git commit 'first part of split commit'
git add file2
git commit 'second part of split commit'
git rebase --continue

#-------------------------------------------
# Modify recent commit
#-------------------------------------------
# Edit hello.py and main.py
git add hello.py
git commit
 
# Realize you forgot main.py
git add main.py
git commit --amend --no-edit
 
# hello.py and main.py in same commits


#-------------------------------------------
# XX
#-------------------------------------------
git commit -a -m 'commit 1'
git commit -a -m 'commit 2'
git reset --hard HEAD~2               // remove recent 2 commits

#-------------------------------------------
# Undo a commit
#-------------------------------------------
git commit ... // commit done
git reset --soft 'HEAD^'
... edit ...
git add ..
git commit -c ORIG_HEAD


#-------------------------------------------
# XX
#-------------------------------------------

{% endhighlight %}



### Git Flow Sample
{% highlight bash %}
# master, develop, release, hotfix, feature
 
# init develop branch
git branch develop
git push -u origin develop
 
# developers init local copy
git clone ssh://user@host/path/to/repo.git
git checkout -b develop origin/develop
 
# ------------------------------------------
# feature development
# ------------------------------------------
# begin with new feature
git checkout -b feature-helloworld develop
... modify
git status
git add <some-file>
git commit -m 'log msg'
 
# finish the feature
git pull origin develop
git checkout develop              // switch branch
git merge feature-helloworld
git push
git branch -d feature-helloworld  // drop feature branch
 
# ------------------------------------------
# release
# ------------------------------------------
git checkout -b release-0.1 develop
...bugfix and ready to ship
 
# merge to master
git checkout master
git merge release-0.1
git push
 
# merge to develop
git checkout develop
git merge release-0.1
git push
git branch -d release-0.1        // drop branch, if needed
 
# tag
git tag -a 0.1 -m "Initial public release" master
git push --tags
 
# ------------------------------------------
# production bug fix
# ------------------------------------------
git checkout -b issue-#001 master
... fix bug
git checkout master
git merge hotfix-#001
git push
git checkout develop
git merge hotfix-#001
git push
git branch -d hotfix-#001
{% endhighlight %}


### Where is the git configuration file
{% highlight bash %}
# Repo specific (HIGH)
<repo>/.git/config
 
# User specific
~/.gitconfig
 
# System wide (LOW)
/etc/gitconfig
{% endhighlight %}


### How to setup a workspace only includes what you want?
As of `Git 1.7`
{% highlight bash %}
# 1. clone
git clone git @bitbucket.org:Propellerhead/uconnect.git

# 2. open feature
git config core.sparsecheckout true
 
# 3. add the module or files you want
echo build.gradle > .git/info/sparse-checkout
echo settings.gradle >> .git/info/sparse-checkout
echo nz.co.uconnect.config >> .git/info/sparse-checkout
 
# 4. take effect
git read-tree -m -u HEAD



# You can also EXCLUDE something
#   .git/info/sparse-checkout
*
!unwanted

# How to get all back?
#   Deleting sparse-checkout file WON'T work, you must, modify it as
*



{% endhighlight%}



### .gitignore
#### Description
{% highlight text %}
# THIS IS COMMENT
*.a       # ignore all files which name ends with ".a"
!lib.a    # keep lib.a
/TODO     # ignore file "TODO" under root dir (exclude subdir/TODO)
build/    # ignore any folder named "build"
doc/*.txt # ignore doc/*.txt
/*/.projects # ignore /1stLevelDir/.projects
{% endhighlight %}

#### Sample file
{% highlight text %}
## generic
*~
*.lock
*.DS_Store
*.swp
*.out
*.bak
Thumb.db
buildEclipseProjFile.bat
local_build.txt
how_to_get_ivu_data.txt
#java specific
*.class
*.jar
!gradle-wrapper.jar
*.class
*.war
*.par
.dbshell
# ignore build folders
build
target
bin
classes
dist
temp
servers
common.core/generated/
common.build/deployment/old/
#eclipse specific
.classpath
.project
.sonar
.settings
.externalToolBuilders
.ant-targets-build.xml
.metadata
#idea
.idea
*.iml
# ivy
ivy-cache
#gradle
.gradle
artifacts
artifacts.xml
gradle-local.properties
localRepository
#merge
*.orig
*.log
{% endhighlight %}


## On-off setup
Configuring Git 
{% highlight bash %}
git config --global user.name "Vic Liu"
git config --global user.email "vic.liu@xx.yy"
git config --global color.ui auto
{% endhighlight %}




## Linux

### Commands
{% highlight bash %}
# chmod
# 1:Exe, 2:Write, 4:Read
# 664: Owner-RW, Group-R, Other-R
chmod 644 file.html
# 755: Owner-ERW, Group-ER, Other-ER
$ chmod 755 file.html

#
# diff files
#
sdiff file1.txt file2.txt
sdiff dir1/ dir2/

#
# unzip specific file
#
unzip -p my.zip path/to/zipped/file.txt > theFile.txt
unzip -p my.zip path/to/zipped/file.txt // to console


#
# tcpdump
#
tcpdump -vvnnSX dst port 5006 and dst host 172.31.225.100

#
# list file sorted by date descending
#
ls -rt

#
# pretty-print XML
#
cat xyz.xml | xmllint --format - | less

#
# create dummy big file
#
fallocate -l 50M big.zip

#
# Linux distribution / kernel 
#
lsb_release -a
uname -a
uname -mrs

#
# content only from file 2
#
grep -Fvxf file1 file2


{% endhighlight %}


## Code snippet
### Spring: create JNDI naming context
{% highlight java %}
public CoreDaoTestBase() {
    EmbeddedDatabase dbRtJbossTisp = new EmbeddedDatabaseBuilder().setType(EmbeddedDatabaseType.HSQL).setName("db-test-rtJbossTisp").build();
    EmbeddedDatabase dbDwStaging = new EmbeddedDatabaseBuilder().setType(EmbeddedDatabaseType.HSQL).setName("db-test-dwArtaStaging").build();
    SimpleNamingContextBuilder builder = new SimpleNamingContextBuilder();
    builder.bind("java:/RealtimeJbossTispDS", dbRtJbossTisp);
    builder.bind("java:/DwArtaStagingDS", dbDwStaging);
     
    try {
        builder.activate();
    } catch (IllegalStateException ise) {
        throw new RuntimeException(ise);
    } catch (NamingException ne) {
        throw new RuntimeException(ne);
    }
}
{% endhighlight %}



## Misc

### Source Layout
http://repo.springsource.org/release/org/springframework/spring-library/
{% highlight text %}
[project name]/
    src/
        main/
            java/           java
            scala/          scala; any other lanuages, go at the same level, etc. groovy
            resources/      resources for the project
            filters/        resorces filter files
            assembly/       assembly descriptor
            config/         configuration files
            webapp/         web app
                WEB-INF/
                    jsp/

        test/
            java/           test sources
            resource/       test resources
            filters/        test resource filters
        site/               project web site source
        doc/                other docs
    lib/                    if not using Maven/Gradle, better checkin the dependent 3rd party libs
        runtime/            lib required at runtime
        test/               lib required to run tests
        build/              lib required to build the project
{% endhighlight %}


### Spring lib description
{% highlight text %}
spring.jar              //include all classes except spring-mock.jar
 
// Required
spring-core.jar         //framework + util
spring-beans.jar        //access configuration file, manage bean and IoC related
 
// Optional
spring-aop.jar          //AOP feature, e.g. declarative trans management
spring-context.jar      //Extension: ApplicationContext,JNDI,UIIntegration(Velocity,Freemarker..), Validation
 
spring-dao.jar          //Spring DAO, Transaction
spring-hibernate.jar    //integrate with Hibernate
spring-jdbc.jar         //integrate with JDBC
spring-orm.jar          //DAO extension, e.g. iBATIS, JDO, OJB, TopLink; relises on spring.dao.jar
 
spring-remoting.jar     //Remote call, EJB, JMS, RMI, Hessian, JAX-RPC, etc.
spring-support.jar      //Cache,JCA,JMX,JavaMail,Scheduling,etc.
spring-web.jar          //Core classes touched by web development, include auto load WebApplicationContext
spring-webmvc.jar       //Spring MVC framework, i18n, tag, theme,..
spring-mock.jar         //test lib
 
spring-context
  spring-aop
    spring-beans
      spring-core
 
spring-dao [core,bean,aop,context]
spring-web [core,bean,context]
spring-webmvc [core,bean,context,web]
spring-hibernate [core,bean,aop,dao,jdbc,orm,web,webmvc]
{% endhighlight %}


### Availability & Downtime
|Availability %|Downtime per year|Downtime per month*|Downtime per week|
|---|---|---|---|
|90%|36.5 days|72 hours|16.8 hours|
|95%|18.25 days|36 hours|8.4 hours|
|98%|7.30 days|14.4 hours|3.36 hours|
|99%|3.65 days|7.20 hours|1.68 hours|
|99.5%|1.83 days|3.60 hours|50.4 minutes|
|99.8%|17.52 hours|86.23 minutes|20.16 minutes|
|99.9% ("three nines")|8.76 hours|43.2 minutes|10.1 minutes|
|99.95%|4.38 hours|21.56 minutes|5.04 minutes|
|99.99% ("four nines")|52.6 minutes|4.32 minutes|1.01 minutes|
|99.999% ("five nines")|5.26 minutes|25.9 seconds|6.05 seconds|
|99.9999% ("six nines")|31.5 seconds|2.59 seconds|0.605 seconds|



### NodeJS
* 2 spaces for indention
* Use \n
* No tailing whitespaces
* Use ;
* Use single quote
* Declare one variable per line
* use lowerCamelCase for variable, properties and function names
* use UpperCamelCase for class names
* use UPPERCASE for constant
* Method chaining
{% highlight javascript %}
user
  .findOne({ name: 'foo' })
  .populate('bar')
  .exec(function(err, user) {
    return true;
  });
{% endhighlight %}
* Name yoru closures
{% highlight javascript %}
req.on('end', function onEnd() {
  console.log('winning');
});
{% endhighlight %}
* Crazy shit need to stay away from: `Object.freeze`,`Object.preventExtensions`,`Object.seal`, `with`, `eval`
* add use `restrict` to each JS file
* err is the first parameter





### Good Sublime Plugins
* DocBlockr - https://github.com/spadgos/sublime-jsdocs
* Markdown preview - https://github.com/revolunet/markdown-preview
* Git - https://github.com/kemayo/sublime-text-git
* GitGutter - https://github.com/jisaacks/GitGutter
* Emmet - quick html/js/css code generater
* SublimeCodeIntel - code hint
* TrailingSpaces - show trailing space


## Templates
### web.xml (servlet 2.5)
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<web-app id="AVL_RAPID_Service" version="2.5"
    xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <filter>
        <filter-name>myFilter</filter-name>
        <filter-class>servlets.SimpleServletFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>myFilter</filter-name>
        <url-pattern>*.simple</url-pattern>
    </filter-mapping>

    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
{% endhighlight %}



{% highlight text %}
{% endhighlight %}

## Groovy
### List
{% highlight groovy %}
// 
// indexOf & lastIndexOf
// 
['a', 'b', 'a', 'c'].lastIndexOf('a')    // 2
['a', 'b', 'a', 'c'].lastIndexOf('d')    // -1
 

// 
// each & eachWithIndex
// 
['a','b','c'].each { elem ->
    println elem
}
 
['k1':'v1', 'k2':'v2'].each { k, v ->
    println "$k = $v"
}
 
// eachWithIndex
['a','b','c'].eachWithIndex { elem, i ->
    println "$i : $elem"
}

// 
// any & every
// 
// any one matches, return TRUE
['a','bb','ccc'].any { elem ->
    elem.length() < 3
}
 
// all match, return TRUE
['a','bb','ccc'].every { elem ->
    elem.length() < 3
}

// 
// grep
// 
// collect
def newList = ['a', 'ab', 'abc'].grep {elem ->
    elem.length() < 3
}
 
// regex
def newList = ['a', 'ab', 'abc'].grep(~/../)
 
// intersection
def list1 = ['a', 'b', 'c']
def list2 = ['x', 'a', 'c']
def inter = list1.grep(list2) // ['a','c']

// 
// join
// 
['2007','10','20'].join('-') // 2007-10-20

// 
// sort
// 
[3, 1, 5].sort() // [1, 3, 5]
['b', 'a', 'c'].sort() // ['a', 'b', 'c']
 
 
// asc, equals to aList.sort()
[3, 1, 5].sort { a, b ->
    a <=> b
} // [1, 3, 5]
 
// desc
[3, 1, 5].sort { a, b ->
    a <=> b
} // [5, 3, 1]

// 
// sort list of customized object
// 
// User { id, name}
[new User(id:8, name:'Tom'), new User(id:5, name:'Jack')].sort { a, b ->
    a.id <=> b.id
} //[user(5), user(8)]

// 
// random sort
// 
[1,2,3,4,5].sort {
    Math.random()
}

// 
// find & findAll
// 
// first matched element
[1,3,5].find( elem ->
    elem < 5
} // 1
 
// all matched element
[1,3,5].find( elem ->
    elem < 5
} // [1,3]

// 
// collect
// 
// process element and place result into a new collection
def newList = [1, 2, 3].collect { elem ->
    elem * 2
} // [2, 4, 6]
 
 
def newMap = ['k1':'v1', 'k2':'v2'].collect { entry -> 
    "${entry.key}a : ${entry.value}a"
} // ['k1a':'v1a', 'k2a':'v2a']

// 
// groupBy
// 
def list = ['a', 'b', 'abc', 'ab', 'c', 'bc']
list.groupBy { elem ->
    elem.length()
}
 
result: [1:["a", "b", "c"], 2:["ab", "bc"], 3:["abc"]]

//inject
// 
// reverse
// 
['a', 'b', 'c'].reverse() // ['c', 'b', 'a']

// 
// reverse with more process
// 
def listX = ['Tom Cheng','Mike Wang','Jack Deng']
def listY = []
listX.reverseEach{
    def names = it.split(' ')
    listY << names[0]
}
println listY // ['Jack', 'Mike', 'Tom']

// 
// tokenize
// 
'a1/a2/a3'.tokenize('/') // ['a1', 'a2', 'a3']

// 
// unique - remove duplicate element
['a', 'b', 'a', 'b'].unique() // ['a', 'b']
// 
// max & min
[1, 5, 2].max() // 5
[1, 5, 2].min() // 1
// 
// count
['a', 'ab', 'a'].count('a') // 2
'abaa'.count('a') // 3
// 
// sum
[1,2,3].sum() // 6
['a','b','c'].sum() // abc
// 
// intersection
def listX = ['a', 'a', 'b', 'c']
def listY = ['a', 'c', 'c']
 
listX.intersect(listY) // ['a', 'c', 'c']
 
listX.unique().intersect(listY.unique()) // ['a', 'c']
// 
// union
def listX = ['a', 'a', 'b', 'c']
def listY = ['a', 'c', 'c']
 
listX+listY // ['a', 'a', 'b', 'c', 'a', 'c', 'c']
 
(listX + listY).unique() // ['a', 'b', 'c']
// 
// has "union'' not
['a','b','c','d'].disjoint(['e', 'f', 'a']) // false, b/c has Union
['a','b','c','d'].disjoint(['e', 'f', 'g']) // true, b/c NO Union
 
// 
// in, count
println (1 in [1, 2, 3]) // true
println (4 in [1, 2, 3]) // false
 
println ([1, 1, 3].count(1)) // 2
println (['a', 'b', 'a'].count('a')) // 2
println (['a', 'b', 'a'].count('c')) // 2

// 
// containsAll
def listX = ['a', 'b', 'c', 'd']
listX.containsAll(['a', 'd']) // true
listX.containsAll(['a', 'e']) // false
{% endhighlight%}

## Jenkins
### GIT plugin Environment Valirables
* GIT_COMMIT - SHA of the current
* GIT_BRANCH - Name of the branch currently being used, e.g. "master" or "origin/foo"
* GIT_PREVIOUS_COMMIT - SHA of the previous built commit from the same branch (the current SHA on first build in branch)
* GIT_URL - Repository remote URL
* GIT_URL_N - Repository remote URLs when there are more than 1 remotes, e.g. GIT_URL_1, GIT_URL_2
* GIT_AUTHOR_EMAIL - Committer/Author Email
* GIT_COMMITTER_EMAIL - Committer/Author Email

### Groovy script for checking nodes status
run it on http://JENKINS_SERVER/script
{% highlight groovy %}
import hudson.model.*
import hudson.node_monitors.*
import hudson.slaves.*
import java.util.concurrent.*
jenkins = Hudson.instance
 
def getEnviron(computer) {
   def env
   def thread = Thread.start("Getting env from ${computer.name}", { env = computer.environment })
   thread.join(2000)
   if (thread.isAlive()) thread.interrupt()
   env
}
def slaveAccessible(computer) {
    getEnviron(computer)?.get('PATH') != null
}
 
def numberOfflineNodes = 0
def numberNodes = 0
for (slave in jenkins.slaves) {
   def computer = slave.computer
   numberNodes ++
   println ""
   println "Checking computer ${computer.name}:"
   def isOK = (slaveAccessible(computer) && !computer.offline)
   if (isOK) {
     println "\t\tOK, got PATH back from slave ${computer.name}."
     println('\tcomputer.isOffline: ' + slave.getComputer().isOffline());
     println('\tcomputer.isTemporarilyOffline: ' + slave.getComputer().isTemporarilyOffline());
     println('\tcomputer.getOfflineCause: ' + slave.getComputer().getOfflineCause());
     println('\tcomputer.offline: ' + computer.offline);
      
      
   } else {
     numberOfflineNodes ++
     println "  ERROR: can't get PATH from slave ${computer.name}."
     println('\tcomputer.isOffline: ' + slave.getComputer().isOffline());
     println('\tcomputer.isTemporarilyOffline: ' + slave.getComputer().isTemporarilyOffline());
     println('\tcomputer.getOfflineCause: ' + slave.getComputer().getOfflineCause());
     println('\tcomputer.offline: ' + computer.offline);
     //sendMail(computer.name, slave.getComputer().getOfflineCause().toString())
     if (slave.getComputer().isTemporarilyOffline()) {
      if (!slave.getComputer().getOfflineCause().toString().contains("Disconnected by")) {
         computer.setTemporarilyOffline(false, slave.getComputer().getOfflineCause())       
      }
     } else {
         computer.connect(true) 
     }
   }
 }
println ("Number of Offline Nodes: " + numberOfflineNodes)
println ("Number of Nodes: " + numberNodes)
{% endhighlight %}

## Java Time Zone
### Convert
{% highlight java %}
TimeZone timeZone1 = TimeZone.getTimeZone("America/Los_Angeles");
TimeZone timeZone2 = TimeZone.getTimeZone("Europe/Copenhagen");

Calendar calendar = new GregorianCalendar();

long timeCPH = calendar.getTimeInMillis();
System.out.println("timeCPH  = " + timeCPH);
System.out.println("hour     = " + calendar.get(Calendar.HOUR_OF_DAY));

// convert
calendar.setTimeZone(timeZone1);

long timeLA = calendar.getTimeInMillis();
System.out.println("timeLA   = " + timeLA);
System.out.println("hour     = " + calendar.get(Calendar.HOUR_OF_DAY));
{% endhighlight %}


/*
timeHere = 1264881305341
hour     = 20
timeLA   = 1264881305341
hour     = 11
*/

### Time Zone ID List
{% highlight java %}
Etc/GMT+12
Etc/GMT+11
MIT
Pacific/Apia
Pacific/Midway
Pacific/Niue
Pacific/Pago_Pago
Pacific/Samoa
US/Samoa
America/Adak
America/Atka
Etc/GMT+10
HST
Pacific/Fakaofo
Pacific/Honolulu
Pacific/Johnston
Pacific/Rarotonga
Pacific/Tahiti
SystemV/HST10
US/Aleutian
US/Hawaii
Pacific/Marquesas
AST
America/Anchorage
America/Juneau
America/Nome
America/Yakutat
Etc/GMT+9
Pacific/Gambier
SystemV/YST9
SystemV/YST9YDT
US/Alaska
America/Dawson
America/Ensenada
America/Los_Angeles
America/Tijuana
America/Vancouver
America/Whitehorse
Canada/Pacific
Canada/Yukon
Etc/GMT+8
Mexico/BajaNorte
PST
PST8PDT
Pacific/Pitcairn
SystemV/PST8
SystemV/PST8PDT
US/Pacific
US/Pacific-New
America/Boise
America/Cambridge_Bay
America/Chihuahua
America/Dawson_Creek
America/Denver
America/Edmonton
America/Hermosillo
America/Inuvik
America/Mazatlan
America/Phoenix
America/Shiprock
America/Yellowknife
Canada/Mountain
Etc/GMT+7
MST
MST7MDT
Mexico/BajaSur
Navajo
PNT
SystemV/MST7
SystemV/MST7MDT
US/Arizona
US/Mountain
America/Belize
America/Cancun
America/Chicago
America/Costa_Rica
America/El_Salvador
America/Guatemala
America/Managua
America/Menominee
America/Merida
America/Mexico_City
America/Monterrey
America/North_Dakota/Center
America/Rainy_River
America/Rankin_Inlet
America/Regina
America/Swift_Current
America/Tegucigalpa
America/Winnipeg
CST
CST6CDT
Canada/Central
Canada/East-Saskatchewan
Canada/Saskatchewan
Chile/EasterIsland
Etc/GMT+6
Mexico/General
Pacific/Easter
Pacific/Galapagos
SystemV/CST6
SystemV/CST6CDT
US/Central
America/Bogota
America/Cayman
America/Detroit
America/Eirunepe
America/Fort_Wayne
America/Grand_Turk
America/Guayaquil
America/Havana
America/Indiana/Indianapolis
America/Indiana/Knox
America/Indiana/Marengo
America/Indiana/Vevay
America/Indianapolis
America/Iqaluit
America/Jamaica
America/Kentucky/Louisville
America/Kentucky/Monticello
America/Knox_IN
America/Lima
America/Louisville
America/Montreal
America/Nassau
America/New_York
America/Nipigon
America/Panama
America/Pangnirtung
America/Port-au-Prince
America/Porto_Acre
America/Rio_Branco
America/Thunder_Bay
America/Toronto
Brazil/Acre
Canada/Eastern
Cuba
EST
EST5EDT
Etc/GMT+5
IET
Jamaica
SystemV/EST5
SystemV/EST5EDT
US/East-Indiana
US/Eastern
US/Indiana-Starke
US/Michigan
America/Anguilla
America/Antigua
America/Aruba
America/Asuncion
America/Barbados
America/Boa_Vista
America/Campo_Grande
America/Caracas
America/Cuiaba
America/Curacao
America/Dominica
America/Glace_Bay
America/Goose_Bay
America/Grenada
America/Guadeloupe
America/Guyana
America/Halifax
America/La_Paz
America/Manaus
America/Martinique
America/Montserrat
America/Port_of_Spain
America/Porto_Velho
America/Puerto_Rico
America/Santiago
America/Santo_Domingo
America/St_Kitts
America/St_Lucia
America/St_Thomas
America/St_Vincent
America/Thule
America/Tortola
America/Virgin
Antarctica/Palmer
Atlantic/Bermuda
Atlantic/Stanley
Brazil/West
Canada/Atlantic
Chile/Continental
Etc/GMT+4
PRT
SystemV/AST4
SystemV/AST4ADT
America/St_Johns
CNT
Canada/Newfoundland
AGT
America/Araguaina
America/Bahia
America/Belem
America/Buenos_Aires
America/Catamarca
America/Cayenne
America/Cordoba
America/Fortaleza
America/Godthab
America/Jujuy
America/Maceio
America/Mendoza
America/Miquelon
America/Montevideo
America/Paramaribo
America/Recife
America/Rosario
America/Sao_Paulo
Antarctica/Rothera
BET
Brazil/East
Etc/GMT+3
America/Noronha
Atlantic/South_Georgia
Brazil/DeNoronha
Etc/GMT+2
America/Scoresbysund
Atlantic/Azores
Atlantic/Cape_Verde
Etc/GMT+1
Africa/Abidjan
Africa/Accra
Africa/Bamako
Africa/Banjul
Africa/Bissau
Africa/Casablanca
Africa/Conakry
Africa/Dakar
Africa/El_Aaiun
Africa/Freetown
Africa/Lome
Africa/Monrovia
Africa/Nouakchott
Africa/Ouagadougou
Africa/Sao_Tome
Africa/Timbuktu
America/Danmarkshavn
Atlantic/Canary
Atlantic/Faeroe
Atlantic/Madeira
Atlantic/Reykjavik
Atlantic/St_Helena
Eire
Etc/GMT
Etc/GMT+0
Etc/GMT-0
Etc/GMT0
Etc/Greenwich
Etc/UCT
Etc/UTC
Etc/Universal
Etc/Zulu
Europe/Belfast
Europe/Dublin
Europe/Lisbon
Europe/London
GB
GB-Eire
GMT
GMT0
Greenwich
Iceland
Portugal
UCT
UTC
Universal
WET
Zulu
Africa/Algiers
Africa/Bangui
Africa/Brazzaville
Africa/Ceuta
Africa/Douala
Africa/Kinshasa
Africa/Lagos
Africa/Libreville
Africa/Luanda
Africa/Malabo
Africa/Ndjamena
Africa/Niamey
Africa/Porto-Novo
Africa/Tunis
Africa/Windhoek
Arctic/Longyearbyen
Atlantic/Jan_Mayen
CET
ECT
Etc/GMT-1
Europe/Amsterdam
Europe/Andorra
Europe/Belgrade
Europe/Berlin
Europe/Bratislava
Europe/Brussels
Europe/Budapest
Europe/Copenhagen
Europe/Gibraltar
Europe/Ljubljana
Europe/Luxembourg
Europe/Madrid
Europe/Malta
Europe/Monaco
Europe/Oslo
Europe/Paris
Europe/Prague
Europe/Rome
Europe/San_Marino
Europe/Sarajevo
Europe/Skopje
Europe/Stockholm
Europe/Tirane
Europe/Vaduz
Europe/Vatican
Europe/Vienna
Europe/Warsaw
Europe/Zagreb
Europe/Zurich
MET
Poland
ART
Africa/Blantyre
Africa/Bujumbura
Africa/Cairo
Africa/Gaborone
Africa/Harare
Africa/Johannesburg
Africa/Kigali
Africa/Lubumbashi
Africa/Lusaka
Africa/Maputo
Africa/Maseru
Africa/Mbabane
Africa/Tripoli
Asia/Amman
Asia/Beirut
Asia/Damascus
Asia/Gaza
Asia/Istanbul
Asia/Jerusalem
Asia/Nicosia
Asia/Tel_Aviv
CAT
EET
Egypt
Etc/GMT-2
Europe/Athens
Europe/Bucharest
Europe/Chisinau
Europe/Helsinki
Europe/Istanbul
Europe/Kaliningrad
Europe/Kiev
Europe/Minsk
Europe/Nicosia
Europe/Riga
Europe/Simferopol
Europe/Sofia
Europe/Tallinn
Europe/Tiraspol
Europe/Uzhgorod
Europe/Vilnius
Europe/Zaporozhye
Israel
Libya
Turkey
Africa/Addis_Ababa
Africa/Asmera
Africa/Dar_es_Salaam
Africa/Djibouti
Africa/Kampala
Africa/Khartoum
Africa/Mogadishu
Africa/Nairobi
Antarctica/Syowa
Asia/Aden
Asia/Baghdad
Asia/Bahrain
Asia/Kuwait
Asia/Qatar
Asia/Riyadh
EAT
Etc/GMT-3
Europe/Moscow
Indian/Antananarivo
Indian/Comoro
Indian/Mayotte
W-SU
Asia/Riyadh87
Asia/Riyadh88
Asia/Riyadh89
Mideast/Riyadh87
Mideast/Riyadh88
Mideast/Riyadh89
Asia/Tehran
Iran
Asia/Aqtau
Asia/Baku
Asia/Dubai
Asia/Muscat
Asia/Oral
Asia/Tbilisi
Asia/Yerevan
Etc/GMT-4
Europe/Samara
Indian/Mahe
Indian/Mauritius
Indian/Reunion
NET
Asia/Kabul
Asia/Aqtobe
Asia/Ashgabat
Asia/Ashkhabad
Asia/Bishkek
Asia/Dushanbe
Asia/Karachi
Asia/Samarkand
Asia/Tashkent
Asia/Yekaterinburg
Etc/GMT-5
Indian/Kerguelen
Indian/Maldives
PLT
Asia/Calcutta
IST
Asia/Katmandu
Antarctica/Mawson
Antarctica/Vostok
Asia/Almaty
Asia/Colombo
Asia/Dacca
Asia/Dhaka
Asia/Novosibirsk
Asia/Omsk
Asia/Qyzylorda
Asia/Thimbu
Asia/Thimphu
BST
Etc/GMT-6
Indian/Chagos
Asia/Rangoon
Indian/Cocos
Antarctica/Davis
Asia/Bangkok
Asia/Hovd
Asia/Jakarta
Asia/Krasnoyarsk
Asia/Phnom_Penh
Asia/Pontianak
Asia/Saigon
Asia/Vientiane
Etc/GMT-7
Indian/Christmas
VST
Antarctica/Casey
Asia/Brunei
Asia/Chongqing
Asia/Chungking
Asia/Harbin
Asia/Hong_Kong
Asia/Irkutsk
Asia/Kashgar
Asia/Kuala_Lumpur
Asia/Kuching
Asia/Macao
Asia/Macau
Asia/Makassar
Asia/Manila
Asia/Shanghai
Asia/Singapore
Asia/Taipei
Asia/Ujung_Pandang
Asia/Ulaanbaatar
Asia/Ulan_Bator
Asia/Urumqi
Australia/Perth
Australia/West
CTT
Etc/GMT-8
Hongkong
PRC
Singapore
Asia/Choibalsan
Asia/Dili
Asia/Jayapura
Asia/Pyongyang
Asia/Seoul
Asia/Tokyo
Asia/Yakutsk
Etc/GMT-9
JST
Japan
Pacific/Palau
ROK
ACT
Australia/Adelaide
Australia/Broken_Hill
Australia/Darwin
Australia/North
Australia/South
Australia/Yancowinna
AET
Antarctica/DumontDUrville
Asia/Sakhalin
Asia/Vladivostok
Australia/ACT
Australia/Brisbane
Australia/Canberra
Australia/Hobart
Australia/Lindeman
Australia/Melbourne
Australia/NSW
Australia/Queensland
Australia/Sydney
Australia/Tasmania
Australia/Victoria
Etc/GMT-10
Pacific/Guam
Pacific/Port_Moresby
Pacific/Saipan
Pacific/Truk
Pacific/Yap
Australia/LHI
Australia/Lord_Howe
Asia/Magadan
Etc/GMT-11
Pacific/Efate
Pacific/Guadalcanal
Pacific/Kosrae
Pacific/Noumea
Pacific/Ponape
SST
Pacific/Norfolk
Antarctica/McMurdo
Antarctica/South_Pole
Asia/Anadyr
Asia/Kamchatka
Etc/GMT-12
Kwajalein
NST
NZ
Pacific/Auckland
Pacific/Fiji
Pacific/Funafuti
Pacific/Kwajalein
Pacific/Majuro
Pacific/Nauru
Pacific/Tarawa
Pacific/Wake
Pacific/Wallis
NZ-CHAT
Pacific/Chatham
Etc/GMT-13
Pacific/Enderbury
Pacific/Tongatapu
Etc/GMT-14
Pacific/Kiritimati
{% endhighlight %}
