# Using the Maven Tomcat plugin to build an executable jar

Follow each step to build an app from scratch, or skip to the end get the source for this article. You can also use almost any existing Maven webapp project.

## Prerequisites

* Basic Java knowledge, including an installed version of the JVM and Maven.
* Basic Git knowledge, including an installed version of Git.

## The Tomcat Maven Plugin

The [Tomcat Maven Plugin](http://tomcat.apache.org/maven-plugin.html) makes it easy to run and work with webapps that you intend to deploy on Tomcat. As of version 2.0 (still in beta) it also allows you to build your project into an executable jar or war file. Once you do this you can run your application from the command line like this:

    $ java -jar target/project-exec-war.jar -resetExtract 
    Feb 13, 2012 9:40:42 PM org.apache.coyote.AbstractProtocol init
    INFO: Initializing ProtocolHandler ["http-bio-8080"]
    Feb 13, 2012 9:40:42 PM org.apache.catalina.core.StandardService startInternal
    INFO: Starting service Tomcat
    Feb 13, 2012 9:40:42 PM org.apache.catalina.core.StandardEngine startInternal
    INFO: Starting Servlet Engine: Apache Tomcat/7.0.25
    Feb 13, 2012 9:40:42 PM org.apache.catalina.startup.ContextConfig getDefaultWebXmlFragment
    INFO: No global web.xml found
    Feb 13, 2012 9:40:43 PM org.apache.coyote.AbstractProtocol start
    INFO: Starting ProtocolHandler ["http-bio-8080"]

The webapp will be loaded into an embedded Tomcat container and launched.

## Create an application if you don't already have one

    :::term
    $ mvn archetype:generate -DarchetypeArtifactId=maven-archetype-webapp
    ...
    [INFO] Generating project in Interactive mode
    Define value for property 'groupId': : com.example
    Define value for property 'artifactId': : helloworld
    
(you can pick any groupId or artifactId). You now have a complete Java web app in the `helloworld` directory.

## Add the Tomcat Maven plugin to your project

Add the plugin to your pom and configure it to build an executable jar file during the package phase by using the pom snippet below:

    :::xml
    <build>
      ...
      <plugins>
        ...    
        <plugin>
          <groupId>org.apache.tomcat.maven</groupId>
          <artifactId>tomcat7-maven-plugin</artifactId>
          <version>2.0-beta-1</version>
          <executions>
            <execution>
              <id>tomcat-run</id>
              <goals>
                <goal>exec-war-only</goal>
              </goals>
              <phase>package</phase>
              <configuration>
                <path>/helloworld</path>
              </configuration>
            </execution>
          </executions>
        </plugin>        
      </plugins>
    </build>

## Run your Application

To build your application simply run:

    :::term
    $ mvn package

And then run your app using the java command:

    :::term
    $ java -jar target/helloworld-1.0-SNAPSHOT-war-exec.jar -resetExtract 

That's it. Your application should start up on port 8080.

You can view more launch configuration options [here](http://tomcat.apache.org/maven-plugin-2.0-beta-1/executable-war-jar.html).The resetExtract flag is important because it clears out the temporary extract directory and avoids inconsistencies between launches.

## Deploy your Application to Heroku

## Create a Procfile

You declare how you want your application executed in `Procfile` in the project root. Create this file with a single line:

    :::term
    web:    java $JAVA_OPTS -jar target/helloworld-1.0-SNAPSHOT-war-exec.jar --httpPort $PORT

<div class="callout" markdown="1">You won't need -resetExtract when running on Heroku because the temporary directory will be cleared automatically</div>

## Deploy to Heroku

Commit your changes to Git:

    :::term
    $ git init
    $ git add .
    $ git commit -m "Ready to deploy"

Create the app on the Cedar stack:

    :::term
    $ heroku create --stack cedar
    Creating high-lightning-129... done, stack is cedar
    http://high-lightning-129.herokuapp.com/ | git@heroku.com:high-lightning-129.git
    Git remote heroku added

Deploy your code:

    :::term
    Counting objects: 227, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (117/117), done.
    Writing objects: 100% (227/227), 101.06 KiB, done.
    Total 227 (delta 99), reused 220 (delta 98)

    -----> Heroku receiving push
    -----> Java app detected
    -----> Installing Maven 3.0.3..... done
    -----> Installing settings.xml..... done
    -----> executing .maven/bin/mvn -B -Duser.home=/tmp/build_1jems2so86ck4 -s .m2/settings.xml -DskipTests=true clean install
           [INFO] Scanning for projects...
           [INFO]       
           ...
           [INFO] 
           [INFO] --- maven-war-plugin:2.1.1:war (default-war) @ warSample ---
           [INFO] Packaging webapp
           [INFO] Assembling webapp [warSample] in [/Users/johnsimone/dev/gitrepos/war-sample/target/warSample-1.0-SNAPSHOT]
           [INFO] Processing war project
           [INFO] Copying webapp resources [/Users/johnsimone/dev/gitrepos/war-sample/src/main/webapp]
           [INFO] Webapp assembled in [28 msecs]
           [INFO] Building war: /Users/johnsimone/dev/gitrepos/war-sample/target/warSample-1.0-SNAPSHOT.war
           [INFO] WEB-INF/web.xml already added, skipping
           [INFO] 
           [INFO] --- tomcat7-maven-plugin:2.0-beta-1:exec-war-only (tomcat-run) @ warSample ---
           Downloading: http://repo1.maven.org/maven2/org/apache/tomcat/maven/common-tomcat-maven-plugin/2.0-beta-1/common-tomcat-maven-plugin-2.0-beta-1.pom
           Downloaded: http://repo1.maven.org/maven2/org/apache/tomcat/maven/common-tomcat-maven-plugin/2.0-beta-1/common-tomcat-maven-plugin-2.0-beta-1.pom (4 KB at 10.1 KB/sec)
           Downloading: http://repo1.maven.org/maven2/org/apache/tomcat/maven/tomcat7-war-runner/2.0-beta-1/tomcat7-war-runner-2.0-beta-1.pom
           Downloaded: http://repo1.maven.org/maven2/org/apache/tomcat/maven/tomcat7-war-runner/2.0-beta-1/tomcat7-war-runner-2.0-beta-1.pom (4 KB at 8.7 KB/sec)
           Downloading: http://repo1.maven.org/maven2/org/apache/tomcat/maven/common-tomcat-maven-plugin/2.0-beta-1/common-tomcat-maven-plugin-2.0-beta-1.jar
           Downloading: http://repo1.maven.org/maven2/org/apache/tomcat/maven/tomcat7-war-runner/2.0-beta-1/tomcat7-war-runner-2.0-beta-1.jar
           Downloaded: http://repo1.maven.org/maven2/org/apache/tomcat/maven/common-tomcat-maven-plugin/2.0-beta-1/common-tomcat-maven-plugin-2.0-beta-1.jar (33 KB at 46.7 KB/sec)
           Downloaded: http://repo1.maven.org/maven2/org/apache/tomcat/maven/tomcat7-war-runner/2.0-beta-1/tomcat7-war-runner-2.0-beta-1.jar (20 KB at 19.6 KB/sec)
           [INFO] ------------------------------------------------------------------------
           [INFO] BUILD SUCCESS
           [INFO] ------------------------------------------------------------------------
           [INFO] Total time: 8.037s
           [INFO] Finished at: Mon Feb 13 22:03:06 PST 2012
           [INFO] Final Memory: 11M/81M
           [INFO] ------------------------------------------------------------------------
    -----> Discovering process types
           Procfile declares types -> web
    -----> Compiled slug size is 62.7MB
    -----> Launching... done, v5
           http://high-lightning-129.herokuapp.com deployed to Heroku

Congratulations! Your web app should now be up and running on Heroku. Open it in your browser with:

    :::term  
    $ heroku open

## Clone the Source

If you want to skip the creation steps you can clone the finished sample:

    $ git clone git@github.com:heroku/devcenter-tomcat-maven-plugin.git
