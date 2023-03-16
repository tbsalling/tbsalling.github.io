---
title: "Thomas Borg Salling"
date: 2018-12-11
categories: AIS
---

{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

# Introducing Java modules in AISmessages
_(Posted {{page.date}})_

# Motivation

AISmessages was born in 2011. It was originally written for Java 7 and later upgraded for Java 8 (lambda expressions). Now is the time to upgrade it for Java 9 – with the biggest new feature being an encapsulation mechanism called modules.

Java 11 is already out – and Java 12 is just around the corner. But a lot of organizations are still at Java 8 considering an upgrade to Java 9 – and getting from 8 to 9 seems like to biggest hurdle for many, because it may require some reorganization of source code – and the understanding of a new concept as opposed to “just” a few new keywords.

This blog is a brief walk-through of the steps I want through to migrate AISmessage from Java 8 to become a Java 9 module. 

# Introduction to modules

A Java module is a new way to modularize applications and to express dependencies between modules.

A module encapsulates a number of classes and makes just a subset of these visible outside of the module. In this way a module can hide a number of classes for its internal implementation of functionality – and only expose the few necessary classes as an API to the outside world. The modularity provided by this mechanism is a lot stronger than what could previously be achieved using visibility modifiers such as public, private and protected. A module is also said to “encapsulate” its implementation.

It is possible to explicitly name each module and to state its dependencies. 

In this post I am not going to compete with the large number of thorough and well-written introductions to Java 9 modules which is available for free on the Internet. Instead I will focus on how modules are applied to AISmessages.

All formal details of Java 9 modules can be found in [JCP-376](https://www.jcp.org/en/jsr/detail?id=376).

And a very good introduction is provided by Mark Reinhold in [“The State of the Module System“](http://openjdk.java.net/projects/jigsaw/spec/sotms/). Jakob Jenkov has also written a good tutoral on [“Java Modules“](http://tutorials.jenkov.com/java/modules.html), and so has Baeldung with [“A Guide to Java 9 Modularity“](https://www.baeldung.com/java-9-modularity).

# Introducing modules in AISmessages

## module-info.java

AISmessages will only contain a single Java module. Therefore we can use the compiler’s “single module mode” as specified in [JEP-261](https://openjdk.java.net/jeps/261). What this means is, that we can introduce modules simply by adding a file called module-info.java in the src/main/java folder. No other reorganization of files or folders is required.

Projects consisting of multiple module do not have this advantage – they need to reorganize their folder structure a bit, so that each Java module gets its own top level folder named after the module.

The module-info.java file initially looks like this:

{% highlight java %}
module dk.tbsalling.ais.messages {
}
{% endhighlight %}

This declares a new Java 9 module with the name of dk.tbsalling.ais.messages. This alone is not very useful – for other Java applications to be able to use packages inside the dk.tbsalling.ais.messages module we need to declare these packages for export. AISmessages has never been structure with this in mind – so we may need to reorganize the packages and classes of AISmessages a bit – but for now we will start by exporting the dk.tbsalling.aismessages package, which is done like this:

{% highlight java %}
module dk.tbsalling.ais.messages {
    exports dk.tbsalling.aismessages;
}
{% endhighlight %}

Notably the module declaration does not contain any requires keywords, which would express dependencies to other modules. This is a hallmark feature of AISmessages.

## Migrating code to Java 9 standard modules

Now if we compile AISmessages with this – an important thing is discovered: In file AISMessage.java there are import statements for package java.beans.IntrospectionException – which now resides in module `java.beans.IntrospectionException`. This is not one of the Java standard modules (which can be seen with java --list-modules) and would therefore require a requires keyword in the module-info.java file. Since this is unwanted by design method dataFields() in the AISMessage.java file was modified to avoid dependencies to classes outside the standard Java modules. The changes to can be seen in [commit b93c06a4](https://github.com/tbsalling/aismessages/commit/b93c06a4ab6315f463b4ecab95ba20ca930312c7).  

Next it would be discovered from compiler errors, that AISmessages also depends on package java.util.logging – which now resides in module java.logging. This is also not one of the standard Java modules. Luckily Java 9 contains [a new logging mechanism](http://openjdk.java.net/jeps/264) to replace good old java.util.logging (JUL) – and this new logging mechanism is contained in the standard modules. Hence a lot of classes in AISmessages were migrated from JUL to Java 9 logging. The actual code changes are contained in commits [4c197d39](https://github.com/tbsalling/aismessages/commit/4c197d39e0f2e0cb00349b252c619f0a6b5c4ac2) and [d7537a67](https://github.com/tbsalling/aismessages/commit/d7537a6752e3167e32e69747d3be1f29405ba015).

## Creating a demo application

AISmessages was never designed with modules in mind. But now with module-info.java in place we need to think about which exact classes need to be exported, and which not. One way to do this is a create a demo application depending on and using AISmessages. It could have the following structure:

{% highlight shell %}
aisdemo tbsalling$ tree 
.
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   ├── dk
│   │   │   │   └── tbsalling
│   │   │   │       └── ais
│   │   │   │           └── demo
│   │   │   │               └── DemoApp.java
│   │   │   └── module-info.java
{% endhighlight %}

where `pom.xml` has the following contents:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>dk.tbsalling.ais</groupId>
    <artifactId>demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>9</source>
                    <target>9</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <packaging>jar</packaging>
    <name>aisapp</name>
    <description>AISDemo is a demo application for AISmessages</description>
    <url>http://tbsalling.dk</url>
    <dependencies>
        <dependency>
            <groupId>dk.tbsalling</groupId>
            <artifactId>aismessages</artifactId>
            <version>3.0.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
{% endhighlight %}

The `maven-compiler-plugin` is explicitly stated because we need a version newer than 3.7.0 to work with modules – and also maven itself must be newer than 3.5.0.

The demo application is itself a module – and it must be declared to depend on AISmessages; hence its `module-info.java` file looks like:

{% highlight java %}
module dk.tbsalling.ais.demo {
    requires dk.tbsalling.ais.messages;
}
{% endhighlight %}

And finally the demo project contains a single class which will flow a number of NMEA armoured AIS messages into AISmessages for decoding:

{% highlight java %}
package dk.tbsalling.ais.demo;

import dk.tbsalling.aismessages.AISInputStreamReader;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;

public class DemoApp {

    public static void main(String[] args) {
        new DemoApp().runDemo();
    }

    public void runDemo() {
        InputStream inputStream = new ByteArrayInputStream(demoNmeaStrings.getBytes());
        System.out.println("AISMessages Demo App");
        System.out.println("--------------------");
        AISInputStreamReader streamReader = new AISInputStreamReader(inputStream, aisMessage ->
                System.out.println("Received AIS message from MMSI " + aisMessage.getSourceMmsi().getMMSI() + ": " + aisMessage)
        );
        try {
            streamReader.run();
        } catch (IOException e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }

    private final String demoNmeaStrings = new String(
            "!AIVDM,1,1,,A,18UG;P0012G?Uq4EdHa=c;7@051@,0*53\n" +
                    "!AIVDM,2,1,1,,539L8BT29ked@90F220I8TE<h4pB22222222220o1p?4400Ht00000000000,0*49\n" +
                    "!AIVDM,2,2,1,,00000000008,2*6C\n" +
                    "!AIVDM,1,1,,A,15NIrB0001G?endE`CpIgQSN08K6,0*02\n" +
                    "!AIVDM,1,1,,B,152Hn;?P00G@K34EWE0d>?wN28KB,0*12\n" +
                    ...
                    "!AIVDM,1,1,,A,152SGj001to?TvlEg4`H?6UL08Jo,0*36"
    );

}
{% endhighlight %}

The complete implementation of the demo application is available on github: <https://github.com/tbsalling/aisdemo>.

## Exporting proper packages

For the demo application to work it quickly becomes evident that AISmessages needs to export the following packages:

{% highlight java %}
module dk.tbsalling.ais.messages {
    exports dk.tbsalling.aismessages;
    exports dk.tbsalling.aismessages.ais;
    exports dk.tbsalling.aismessages.ais.exceptions;
    exports dk.tbsalling.aismessages.ais.messages;
    exports dk.tbsalling.aismessages.ais.messages.types;
    exports dk.tbsalling.aismessages.ais.messages.asm;
}
{% endhighlight %}

so the `module-info.java` file of AISmessages has been extended to accomodate this. This has the side-effect that the socket-based classes nmea/NMEAMessageSocketClient.java and demo/SocketDemoApp.java are no longer reachable from outside the module. This is acceptable because the main entry point to AISmessages nowadays is AISInputStreamReader.java. The other two have therefore been marked as deprecated and will at some point be deleted or moved to the new aisdemo project.

# Availability

The version of AISmessages upgraded to be a Java 9 module is available from

* Github (source code) <https://github.com/tbsalling/aismessages/tree/aismessages-3.0.2>

* maven.org (compiled binaries) <https://search.maven.org/artifact/dk.tbsalling/aismessages/3.0.2/jar>

# Conclusion / wrap-up

This post describes to process of migrating AISmessages to become a Java 9 module and demonstrated that it works using a small demo application.

The last commit prior to the introduction of Java 9 is tagged last-java8-commit.

The following files were changed in the introduction of AISmessages as a Java 9 module:

{% highlight text %}
Created
src/main/java/module-info.java
Updated
pom.xml
src/main/java/dk/tbsalling/aismessages/ais/messages/AISMessage.java
src/main/java/dk/tbsalling/aismessages/ais/messages/DataLinkManagement.java
src/main/java/dk/tbsalling/aismessages/ais/messages/types/SOTDMACommunicationState.java
src/main/java/dk/tbsalling/aismessages/nmea/NMEAMessageHandler.java
src/main/java/dk/tbsalling/aismessages/nmea/NMEAMessageInputStreamReader.java
src/main/java/dk/tbsalling/aismessages/nmea/NMEAMessageSocketClient.java
src/test/java/dk/tbsalling/aismessages/ais/messages/AidToNavigationReportTest.java
{% endhighlight %}
