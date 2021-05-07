Turn Debug mode off in Apache tomcat (UNIX)
========
INTRODUCTION
------
This document will be divided in 4 steps :
* Find out what your CATALINA_HOME directory is
* Turn off or on debug mode for Tomcat Logging
* Turn off or on debug mode for log4j on Apache Tomcat


Find out what your CATALINA_HOME directory is
---------
If you already know what your CATALINA_HOME is, go to Step 2 or 3 accordingly, if you don't, run the command below :
```
ps aux | grep "catalina.home" --color=always
```

This should highlight the directory where $CATALINA_HOME is declared at (find catalina.home on the snippet below and it's being assigned to the CATALINA_HOME directory)

```
jcroyoaun   18754  4.1  1.4 4912236 950636 ?      Sl   09:09   4:12 /bin/java/Linux/64/1.6.0_14/bin/java -Duser.dir=/some/dir/appsvr/webapps/ROOT/WEB-INF -Dlog.dir=/some/dir/appsvr/logs -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode -XX:CMSIncrementalDutyCycleMin=0 -XX:CMSIncrementalDutyCycle=10 -XX:MaxNewSize=128m -XX:NewSize=128m -server -Xms4g -Xmx4g -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:-TraceClassUnloading -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file=/some/dir/appsvr/conf/logging.properties -Djava.endorsed.dirs=/some/dir/appsvr/endorsed -classpath :/home/jcroyoaun/appsvr/bin/bootstrap.jar -Dcatalina.base=/some/dir/appsvr -Dcatalina.home=/some/dir/appsvr -Djava.io.tmpdir=/home/jcroyoaun/appsvr/temp org.apache.catalina.startup.Bootstrap start
```

Turn off debug mode for Tomcat Logging
--------------
```
cd $CATALINA_HOME/conf
```

look for logging.properties file and edit out the different logging groups to log according to your needs. Honestly I don't know exactly which of these groups outlined in the example below is responsible for, so I encourage you to try and experiment, or change them all to DEBUG, INFO, or whatever your need is :
```
1catalina.org.apache.juli.FileHandler.level = INFO
1catalina.org.apache.juli.FileHandler.directory = ${catalina.base}/logs
1catalina.org.apache.juli.FileHandler.prefix = catalina.

2localhost.org.apache.juli.FileHandler.level = INFO
2localhost.org.apache.juli.FileHandler.directory = ${catalina.base}/logs
2localhost.org.apache.juli.FileHandler.prefix = localhost.

3manager.org.apache.juli.FileHandler.level = INFO
3manager.org.apache.juli.FileHandler.directory = ${catalina.base}/logs
3manager.org.apache.juli.FileHandler.prefix = manager.

4admin.org.apache.juli.FileHandler.level = INFO
4admin.org.apache.juli.FileHandler.directory = ${catalina.base}/logs
4admin.org.apache.juli.FileHandler.prefix = admin.

5host-manager.org.apache.juli.FileHandler.level = INFO
5host-manager.org.apache.juli.FileHandler.directory = ${catalina.base}/logs
5host-manager.org.apache.juli.FileHandler.prefix = host-manager.

java.util.logging.ConsoleHandler.level = INFO
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
```

* Turn off or on debug mode for log4j on Apache Tomcat
First is that you might want to cd into your CATALINA_HOME directory and look for *.properties, for example you could do :
```
find -type f -name "log4j.properties"
```
if that doesn't work (unlikely) then do
```
find -type f -name *.properties"
```

edit the file to change 
```
log4j.rootCategory=INFO
```

And the section where the logger stuff is declared :
```
# Set the logger priorities
log4j.logger.com.autozone.aftersoft.util.LogConfiguration=INFO
log4j.logger.org.apache=INFO
```


This should be enough to manipulate and go between DEBUG level logging and INFO level logging.