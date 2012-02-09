# Tomcat Maven Plugin Sample Project

Webapp project that uses the tomcat maven plugin executable war/jar functionality to build to an executable jar file. The project contains an index.jsp that uses a taglib and a simple servlet.

## Building

To build the project run:

    $ mvn package

## Execute

    $ java -jar target/warSample-1.0-SNAPSHOT-war-exec.jar --resetExtract 

