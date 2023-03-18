---
title: "Thomas Borg Salling"
date: 2018-09-24
categories: AIS
---

{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

# Creating, sharing and running a Docker image to decode AIS messages
_(Posted {{page.date}})_

Recently, [I showed](./blog_001_ais_decoder_spring_boot.html) how to use [AISMessages](https://github.com/tbsalling/aismessages) to quickly build a Spring Boot based HTTP/JSON-service capable of converting NMEA-armoured AIS messages into JSON-based parsed AIS messages ([what are AIS messages?](./blog_000_ais.html). Now we want to make it even easier to get this AIS decoder service running, by building and sharing a Docker image of the service, which can easily be downloaded and spun up by anyone using Docker.

# Getting ready

To get going we first want to make sure, that Docker is installed. For our purposes, we will use [Docker for Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac). So go and grab that if you don’t already have it. If you are preferring a certain package manager or are using a different operating system, you will have to get Docker for that – take a look at [“Get Started with Docker”](https://www.docker.com/get-started).

With Docker properly installed, you should be able to run:

{% highlight shell %}
$ docker version
{% endhighlight %}

and get a sensible output.

Second we clone the source code of our AISdecoder into a folder on the local harddrive:

{% highlight shell %}
$ git clone https://github.com/tbsalling/aisdecoder.git
{% endhighlight %}

For the sake of getting the repository in the right starting state, we will rewind it a bit to the following commit:

{% highlight shell %}
$ git checkout 7c02cbcef2ff273ab157e41fa71b193ae3304a93
{% endhighlight %}

And finally we compile the project in order to produce the binary artifact that we will run using Docker:

{% highlight shell %}
$ ./gradlew build
...
BUILD SUCCESSFUL in 4s
5 actionable tasks: 5 executed
{% endhighlight %}

Now we are ready – and this is the file that we want to run in a Docker container:

{% highlight shell %}
$ ls -lh build/libs/
total 32000
-rw-r--r--  1 tbsalling  staff    16M 24 Sep 11:30 aisdecoder-0.0.1-SNAPSHOT.jar
{% endhighlight %}

# Adding the Dockerfile

With all prerequisites in place, the first thing we want to do is to add a `Dockerfile` to the project. The `Dockerfile` describes how Docker should build the Docker image. It could look like this:

{% highlight text %}
FROM openjdk:11-jre-slim
MAINTAINER Thomas Borg Salling "tbsalling@tbsalling.dk"
COPY build/libs/aisdecoder-0.0.1-SNAPSHOT.jar /app/aisdecoder.war
ENTRYPOINT ["java", "-jar", "/app/aisdecoder.war"]
EXPOSE 8080/tcp
{% endhighlight %}

The `FROM` keyword specifies the Docker base image. The rest of the Dockerfile can roughly be considered to be changes or additions to this base image. Base images can be self-built or they can be searched or browsed on e.g. [Docker Hub](https://hub.docker.com/search/?q=java), where contributors upload and share images. We choose to use the `openjdk:11-jre-slim` image from Docker Hub, which is a Linux-based image with the OpenJDK version of Java 11 pre-installed. For a Spring Boot-based Java SE application this is a good start.

The `MAINTAINER` keyword mainly adds meta information to the image and is not very important here.

`COPY` on the other hand is quite important here. It copies the compiled .war-file from your local developer machine into the generated Docker image – and places it in folder /app as aisdecoder.war.

`ENTRYPOINT` defines the executable command, that will be fired by default when running a container based on this image using docker run ... later on. As you can probably see, this is equivalent to java -jar /app/aisdecoder.war – i.e. a command line based launch of our Java SE application.

Finally, `EXPOSE 8080/tcp` tells Docker, that a container launched from this image listens on the specified network port at runtime. In this case in listens for TCP-based network traffic on port 8080 – which is exactly where the embedded Tomcat in our Java SE application listens. So any traffic going to port 8080 of this container will go to our own embedded Tomcat.

To learn more Dockerfile keywords – and details of those used here – it is a good idea to familiarize yourself with the [reference documentation](https://docs.docker.com/engine/reference/builder) for Dockerfiles.

# Building the Docker image

With the Dockerfile in place it is time to actually create the Docker image. This can be done like this:

{% highlight shell %}
$ docker build .
Sending build context to Docker daemon  16.95MB
Step 1/5 : FROM openjdk:11-jre-slim
 ---> 422e4d3c11a7
Step 2/5 : MAINTAINER Thomas Borg Salling "tbsalling@tbsalling.dk"
 ---> Using cache
 ---> 4ce7f868ea8b
Step 3/5 : COPY build/libs/aisdecoder-0.0.1-SNAPSHOT.jar /app/aisdecoder.war
 ---> Using cache
 ---> 7a277936c416
Step 4/5 : ENTRYPOINT ["java", "-jar", "/app/aisdecoder.war"]
 ---> Using cache
 ---> 97e0dee65253
Step 5/5 : EXPOSE 8080/tcp
 ---> Running in a10e415ccf76
Removing intermediate container a10e415ccf76
 ---> 9f37cd551132
Successfully built 9f37cd551132
{% endhighlight %}

This means, that Docker has now successfully built an image identified by `9f37cd551132`.

# Running the Docker image as a container

To spawn a container based on this image, we can issue the following command line:

{% highlight shell %}
$ docker run -p 8080:8080 9f37cd551132
{% endhighlight %}

This instructs Docker to launch a new container based on image `9f37cd551132`. Thanks to the -p option docker will bind to port 8080 on the local host and forward traffic on this port to port 8080 inside the Docker container. In effect, this causes traffic on port 8080 on the host machine to be forwarded to the embedded Tomcat inside our Java SE application running in the Docker container.

# Using the container

So, with our Docker container running, we can now reach its functionality by sending HTTP traffic to port 8080 on our host machine. Like this:

{% highlight shell %}
$ curl -X POST http://localhost:8080/decode -H 'Content-Type: application/json' -d '[ "!AIVDM,1,1,,A,18UG;P0012G?Uq4EdHa=c;7@051@,0*53" ]'
{% endhighlight %}

… which (as we have seen previously) will result in this output:

{% highlight json %}
[{"nmeaMessages":[{"rawMessage":"!AIVDM,1,1,,A,18UG;P0012G?Uq4EdHa=c;7@051@,0*53","valid":true,"sequenceNumber":null,"radioChannelCode":"A","checksum":83,"numberOfFragments":1,"fragmentNumber":1,"messageType":"AIVDM","encodedPayload":"18UG;P0012G?Uq4EdHa=c;7@051@","fillBits":0}],"metadata":{"source":"SRC1","received":"2018-09-24T09:33:45.690187Z","decoderVersion":"2.2.2","category":"AIS"},"repeatIndicator":0,"sourceMmsi":{"mmsi":576048000},"navigationStatus":"UnderwayUsingEngine","rateOfTurn":0,"speedOverGround":6.6,"positionAccuracy":false,"latitude":37.912167,"longitude":-122.42299,"courseOverGround":350.0,"trueHeading":355,"second":40,"specialManeuverIndicator":"NotAvailable","raimFlag":false,"communicationState":{"syncState":"UTCDirect","slotTimeout":1,"numberOfReceivedStations":null,"slotNumber":null,"utcHour":8,"utcMinute":20,"slotOffset":null},"messageType":"PositionReportClassAScheduled","transponderClass":"A","valid":true}]
{% endhighlight %}

# Building a Docker image with a tag

You may have experienced that `9f37cd551132` is not a terribly easy “name” to remember. Actually it is a hash value – and it changes dramatically with every little change to our image. To ease this, Docker supports associating descriptive – easier-to-remember – names with these hashes. So Docker images can be built with a name like this:

{% highlight shell %}
$ docker build -t tbsalling/aisdecoder:latest .
...
Successfully built 9f37cd551132
Successfully tagged tbsalling/aisdecoder:latest
{% endhighlight %}

Here `tbsalling/aisdecoder` is the repository name, and latest is the actual tag name. So now `tbsalling/aisdecoder:latest` points to the image with hash value `9f37cd551132`.

Read more about Docker’s valid tags.

Running a Docker container using a tag

With a tag name in place, a Docker container can be spun up like this:

{% highlight text %}
$ docker run -p 8080:8080 tbsalling/aisdecoder:latest
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.5.RELEASE)
2018-09-24 11:05:20.737  INFO 1 --- [           main] d.t.a.d.a.AisdecoderApplication          : Starting AisdecoderApplication on 8c6771c64973 with PID 1 (/app/aisdecoder.war started by root in /)
2018-09-24 11:05:2
...
{% endhighlight %}

# Uploading an image to Docker hub

With all this work done – wouldn’t it be nice if we could share the result? This would allow anyone to run aisdecoder locally by just downloading our Docker image and run it in a Docker container? Luckily this is possible by uploading our image to Docker Hub.

To do that first make sure, that you have a valid account on https://hub.docker.com. Then, from the command line login like this:

{% highlight shell %}
$ docker login --username=tbsalling
Password: 
Login Succeeded
{% endhighlight %}

With the image already built (see above) we can now push (upload) it to Docker Hub using the repository and tag names like this:

{% highlight shell %}
$ docker push tbsalling/aisdecoder:latest
The push refers to repository [docker.io/tbsalling/aisdecoder]
308058d2da0d: Pushed 
7d2319767e1d: Mounted from library/openjdk 
36fdef6aaa51: Mounted from library/openjdk 
0854ef12fba3: Mounted from library/openjdk 
9a27a9751438: Mounted from library/openjdk 
f9af8abefa4e: Mounted from library/openjdk 
latest: digest: sha256:0f93b0c3b65b794ce628d135515732c0f8a0fa826a8ccb0df9882086cd0d29dd size: 1577
{% endhighlight %}

Now the image is uploaded to Docker Hub. You can see that for yourself by visiting https://hub.docker.com/r/tbsalling/aisdecoder/.

Now let us logout of Docker Hub so that we could be anyone in the public:

{% highlight shell %}
$ docker logout
Removing login credentials for https://index.docker.io/v1/
{% endhighlight %}

Any random person (with Docker installed…) can now pull (download) and run this image as simple as:

{% highlight shell %}
$ docker pull tbsalling/aisdecoder:latest
latest: Pulling from tbsalling/aisdecoder
Digest: sha256:0f93b0c3b65b794ce628d135515732c0f8a0fa826a8ccb0df9882086cd0d29dd
Status: Image is up to date for tbsalling/aisdecoder:latest
$ docker run tbsalling/aisdecoder:latest
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.5.RELEASE)
2018-09-24 11:12:30.158  INFO 1 --- [           main] d.t.a.d.a.AisdecoderApplication          : Starting AisdecoderApplication on 4ab60bf292ce with PID 1 (/app/aisdecoder.war started by root in /)
{% endhighlight %}

# Conclusion

In this post I have shown how to create a Docker image containing an HTTP/JSON-based decoder for NMEA messages with AIS-contents. I showed how to upload and share this image via Docker Hub – and demonstrated how anyone can pull this image and run the AIS decoder from scratch with just 2 command line instructions (provided Docker is already installed).

Have fun 🙂
