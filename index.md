{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

<img src="images/tbs.webp" style="float: right" width="25%" height="25%" />

# Who am I?

* Fulltime freelance programmer and remote worker.
  
* 20+ years of experience as a freelancer.

* University trained engineer (M.Sc.E.E).

* Specialized in Java and open source programming.

* Senior freelance Java architect/developer — AI‑accelerated greenfield & modernization.

* Domain experience across finance, telecom, healthcare, defence and maritime.

* System integration/backend solutions for large and mission-critical systems, sometimes with a twist of applied mathematics.

* Based near Århus, Denmark.

* Contracting for remote work in Scandinavia and Europe – with travel for workshops, team meetings, integration sessions, demos, etc.

# What I do

* Software architecture and backend development (Java)
* System integration and API design (REST/OpenAPI)
* Greenfield delivery: new services, platforms and domain models
* Brownfield delivery: modernization, migrations, refactoring and platform moves
* Cloud/container platforms and observability (e.g. OpenShift, Docker, tracing)

# Greenfield & brownfield

* Greenfield: clarify requirements, establish architecture, build the first production-grade baseline fast.
* Brownfield: reduce risk while modernizing—migrations, modularization, performance, and integration improvements.

# How I work (AI-accelerated)

I use GitHub Copilot and Claude Code as a productivity and quality multiplier for design exploration, implementation, refactoring, tests, and reviews—always with human judgment, code review, and automated testing.

# Confidentiality & responsible AI use

I use AI tools responsibly to accelerate delivery, but I never share confidential client information. I follow client security policies and treat AI output as suggestions that are reviewed and tested before use.

# Open source projects

I am the inventor and maintainer of 

- [AISmessages](https://github.com/tbsalling/aismessages)
- [AISutils](https://github.com/tbsalling/aisutils)

Both projects have commercial users and are related to decoding of safety- and navigation related digital messages from ships. The source code is available on Github and the binaries are in Maven Central.

# Clients

Featured clients that I have worked for:

<img src="images/logo_norlys.svg" style="margin: 15px 15px 15px 15px" />
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
            <p><a href="{{ sm[key].href }}{{ sm[key].id }}" title="{{ sm[key].title }}"><i class="{{ sm[key].fa-icon }} fa-xl"></i> {{ sm[key].id }}</a></p>
        {% endif %}
    {% endfor %}
</div>
{% endif %}

# Recommendations

[Read recommendations](https://www.linkedin.com/in/tbsalling/details/recommendations/) given to me on LinkedIn.

# Availability

Recruiters, enterprises and agencies: please contact me for information on my availability.

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

* AI-accelerated engineering
    * GitHub Copilot
    * Claude Code
    * AI-assisted refactoring, testing and code reviews (human-in-the-loop)

# Posts

* [What is AIS?](./blog_000_ais.html)
* [Creating a Spring Boot based AIS message decoder](./blog_001_ais_decoder_spring_boot.html)
* [Creating, sharing and running a Docker image to decode AIS messages](./blog_002_ais_decoder_docker.html)
* [Running AISdecoder in a Kubernetes cluster on AWS](./blog_003_ais_decoder_kubernetes.html)
* [Introducing Java modules in AISmessages](./blog_004_ais_messages_java_modules.html)
* [Why AISmessages 4.0 made value objects immutable](./blog_005_aismessages_major_v4.html)
* [AIS Application-Specific Messages: Beyond Standard Position Reports](./blog_006_application_specific_messages.html)
