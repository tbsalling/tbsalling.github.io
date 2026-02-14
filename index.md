{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

<img src="images/tbs.webp" style="float: right" width="25%" height="25%" />

# Freelance senior Java architect & developer

I help organizations ship and modernize **mission‑critical Java backend systems** — quickly and safely — through Java, sound architecture, and responsible AI‑assisted engineering.

* **Freelance / remote-first** (based near Århus/Aarhus, Denmark)
* **25+ years** of software development and architecture experience — primarily Java and backend systems
* M.Sc. in Electrical Engineering (M.Sc.E.E.)
* Strong focus on **integration, security, performance, reliability, and operability**
* Broad domain experience in e.g. finance, telecom, healthcare, defence and maritime

## What I do

   - Architecture and delivery of Java backend services (greenfield + modernization)
   - Integration and API delivery (REST/OpenAPI, messaging)
   - Cloud/container deployment (OpenShift/Kubernetes, Docker)
   - Security and performance engineering for backend systems
   - Technical leadership: teaching, mentoring, review, sparring

## How I work

I work AI-accelerated and use **GitHub Copilot** and **Claude Code** as a productivity and quality multiplier for design exploration, implementation, refactoring, tests, and reviews. But I am the human-in-the-loop and I apply **25+ years of Java engineering experience** — to use AI critically: validate assumptions, catch edge cases, and keep designs operable. The final result is always reviewed and tested.

## Open source projects

I am the inventor and maintainer of:

- [AISmessages](https://github.com/tbsalling/aismessages)
- [AISutils](https://github.com/tbsalling/aisutils)

Both projects have commercial users and are related to decoding safety‑ and navigation‑related digital messages from ships. The source code is available on GitHub and the binaries are in Maven Central.

## Clients

Selected clients I have worked for:

<img src="images/logo_norlys.svg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_jb.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_systematic.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_bankdata.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_frequentis.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_dma.jpg" style="margin: 15px 15px 15px 15px" />
<img src="images/logo_nets.jpg" style="margin: 15px 15px 15px 15px" />

## Projects

Read my portfolio of [featured freelance projects](clients.md).

## Getting in touch

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

## Recommendations

[Read recommendations](https://www.linkedin.com/in/tbsalling/details/recommendations/) given to me on LinkedIn.

## Availability

Recruiters, enterprises and agencies: contact me for current availability and a quick discussion of scope, timeline, and collaboration model.

## Technical keywords

**Java & frameworks:** Java SE, Java EE, JPMS (Java modules), MicroProfile, Quarkus, Spring Boot, Hibernate, JPA, CDI, JTA  
**APIs & integration:** REST, OpenAPI, messaging, Apache Camel, NATS, ActiveMQ, AMQP, Keycloak, OIDC, JWT tokens  
**Cloud & ops:** OpenShift, Kubernetes, Docker, Helm, Istio, AWS, Jaeger (distributed tracing)  
**Data:** PostgreSQL, Oracle, MS SQL Server, Cassandra, PostGIS  
**Build & testing:** Maven, Gradle, Jenkins  
**AI‑accelerated engineering:** GitHub Copilot, Claude Code, IDE‑based AI plugins, AI‑assisted refactoring/testing/reviews (human‑in‑the‑loop)

## Posts

* [What is AIS?](./blog_000_ais.html)
* [Creating a Spring Boot based AIS message decoder](./blog_001_ais_decoder_spring_boot.html)
* [Creating, sharing and running a Docker image to decode AIS messages](./blog_002_ais_decoder_docker.html)
* [Running AISdecoder in a Kubernetes cluster on AWS](./blog_003_ais_decoder_kubernetes.html)
* [Introducing Java modules in AISmessages](./blog_004_ais_messages_java_modules.html)
* [Why AISmessages 4.0 made value objects immutable](./blog_005_aismessages_major_v4.html)
* [AIS Application-Specific Messages: Beyond Standard Position Reports](./blog_006_application_specific_messages.html)
