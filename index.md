{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

<img src="images/tbs.webp" style="float: right" width="25%" height="25%" />

# Who am I?

* Fulltime freelance programmer and remote worker.
  
* 20+ years of experience as a freelancer.

* University trained engineer (M.Sc.E.E).

* Specialized in Java and open source programming. 

* Domain experience from banking, government, healthcare, defence, maritime and retail.

* System integration/backend solutions for large and mission-critical systems, sometimes with a twist of applied mathematics.

* Based near Århus, Denmark.

* Contracting for remote work in Scandinavia and Europe – with travel for workshops, team meetings, integration sessions, demos, etc.

# Open source projects

I am the inventor and maintainer of 

- [AISmessages](https://github.com/tbsalling/aismessages)
- [AISutils](https://github.com/tbsalling/aisutils)

Both projects have commercial users and are related to decoding of safety- and navigation related digital messages from ships. The source code is available on Github and the binaries are in Maven Central.

# Clients

Featured clients that I have worked for:

<img src="images/logo_jb.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_systematic.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_bankdata.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_frequentis.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_dma.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_nets.jpg" style="margin: 15px 15px 15px 15px" />

# Projects

Read my portfolio of [featured freelance projects](clients.md).

# Getting in touch

{% if site.data.social-media %}
<div id="social-media">
    {% assign sm = site.data.social-media %}
    {% for entry in sm %}
        {% assign key = entry | first %}
        {% if sm[key].enabled and sm[key].id %}
            <p><a href="{{ sm[key].href }}{{ sm[key].id }}" title="{{ sm[key].title }}"><i class="fa fa-xl {{ sm[key].fa-icon }}"></i> {{ sm[key].id }}</a></p>
        {% endif %}
    {% endfor %}
</div>
{% endif %}

# Recommendations

[Read recommendations](https://www.linkedin.com/in/tbsalling/details/recommendations/) given to me on LinkedIn.

# Availability

Please contact me for information on my availability.

# Technical keywords

* Programming languages and frameworks
    * Java SE
    * Java MP
    * Quarkus
    * Spring Boot
    * Hibernate

* Java API's 
    * JPA
    * CDI
    * JTA

* Cloud technologies
    * OpenShift
    * Infinispan/DataGrid
    * Docker
    * Jaeger
    * Helm

* Databases
    * Oracle
    * Postgres
    * MSSQL
    * Cassandra

* Build tools
    * Gradle
    * Maven

# Posts

* [What is AIS?](./blog_000_ais.html)
* [Creating a Spring Boot based AIS message decoder](./blog_001_ais_decoder_spring_boot.html)
* [Creating, sharing and running a Docker image to decode AIS messages](./blog_002_ais_decoder_docker.html)
* [Running AISdecoder in a Kubernetes cluster on AWS](./blog_003_ais_decoder_kubernetes.html)
* [Introducing Java modules in AISmessages](./blog_004_ais_messages_java_modules.html)