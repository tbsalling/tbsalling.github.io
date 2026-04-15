{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

<img id="profile-portrait" src="images/tbs.webp" style="float: right" width="25%" height="25%" />

# Freelance Java architect & systems integrator

I help organizations **integrate and modernize mission‑critical Java backend systems** — connecting silos, unlocking data flows, and turning fragmented architectures into coherent, high‑value platforms — through Java, sound architecture, and responsible AI‑assisted engineering.

<p class="slogan-body">Systems integrated. Value unlocked.</p>

* **Freelance / remote-first** (based near Århus/Aarhus, Denmark)
* **25+ years** of software development and architecture experience — primarily Java and backend systems
* M.Sc. in Electrical Engineering (M.Sc.E.E.)
* Strong focus on **integration, value delivery, security, performance, reliability, and operability**
* Broad domain experience in e.g. finance, telecom, healthcare, defence and maritime

## What I do

   - Architecture and delivery of Java backend services (greenfield + modernization)
   - System integration and API design (REST/OpenAPI, messaging, event-driven)
   - Cloud/container deployment (OpenShift/Kubernetes, Docker)
   - Security and performance engineering for backend systems
   - Technical leadership: teaching, mentoring, review, sparring

## How I work

I use **GitHub Copilot** and **Claude Code** as productivity multipliers — accelerating integration work, refactoring, and reviews without cutting corners on quality. My relationship with machine learning predates the current wave — my master's thesis explored constrained Markov networks — so I understand the technology behind the tools, not just the tools themselves. Combined with **25+ years of Java experience**, this lets me use AI critically: validate assumptions, catch edge cases, and keep integrated systems operable. All work is reviewed and tested.

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

<div class="tech-keywords">
  <div class="tech-group">
    <span class="tech-category">Java &amp; frameworks</span>
    <div class="tag-list">
      <span class="tag">Java SE</span>
      <span class="tag">Java EE</span>
      <span class="tag">JPMS</span>
      <span class="tag">MicroProfile</span>
      <span class="tag">Quarkus</span>
      <span class="tag">Spring Boot</span>
      <span class="tag">Hibernate</span>
      <span class="tag">JPA</span>
      <span class="tag">CDI</span>
      <span class="tag">JTA</span>
    </div>
  </div>
  <div class="tech-group">
    <span class="tech-category">APIs &amp; integration</span>
    <div class="tag-list">
      <span class="tag">REST</span>
      <span class="tag">OpenAPI</span>
      <span class="tag">Messaging</span>
      <span class="tag">Apache Camel</span>
      <span class="tag">NATS</span>
      <span class="tag">ActiveMQ</span>
      <span class="tag">AMQP</span>
      <span class="tag">Keycloak</span>
      <span class="tag">OIDC</span>
      <span class="tag">JWT</span>
    </div>
  </div>
  <div class="tech-group">
    <span class="tech-category">Cloud &amp; ops</span>
    <div class="tag-list">
      <span class="tag">OpenShift</span>
      <span class="tag">Kubernetes</span>
      <span class="tag">Docker</span>
      <span class="tag">Helm</span>
      <span class="tag">Istio</span>
      <span class="tag">AWS</span>
      <span class="tag">Jaeger</span>
    </div>
  </div>
  <div class="tech-group">
    <span class="tech-category">Data</span>
    <div class="tag-list">
      <span class="tag">PostgreSQL</span>
      <span class="tag">Oracle</span>
      <span class="tag">MS SQL Server</span>
      <span class="tag">Cassandra</span>
      <span class="tag">PostGIS</span>
    </div>
  </div>
  <div class="tech-group">
    <span class="tech-category">Build &amp; testing</span>
    <div class="tag-list">
      <span class="tag">Maven</span>
      <span class="tag">Gradle</span>
      <span class="tag">Jenkins</span>
    </div>
  </div>
  <div class="tech-group">
    <span class="tech-category">AI&#8209;accelerated engineering</span>
    <div class="tag-list">
      <span class="tag">GitHub Copilot</span>
      <span class="tag">Claude Code</span>
      <span class="tag">AI&#8209;assisted refactoring</span>
      <span class="tag">AI&#8209;assisted reviews</span>
      <span class="tag">human&#8209;in&#8209;the&#8209;loop</span>
    </div>
  </div>
</div>

## Posts

* [What is AIS?](./blog_000_ais.html)
* [Creating a Spring Boot based AIS message decoder](./blog_001_ais_decoder_spring_boot.html)
* [Creating, sharing and running a Docker image to decode AIS messages](./blog_002_ais_decoder_docker.html)
* [Running AISdecoder in a Kubernetes cluster on AWS](./blog_003_ais_decoder_kubernetes.html)
* [Introducing Java modules in AISmessages](./blog_004_ais_messages_java_modules.html)
* [Why AISmessages 4.0 made value objects immutable](./blog_005_aismessages_major_v4.html)
* [AIS Application-Specific Messages: Beyond Standard Position Reports](./blog_006_application_specific_messages.html)
