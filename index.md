<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">

## Thomas Borg Salling

I am a fulltime freelance programmer and remote worker with 20+ years of experience. Specialized in Java and open source programming. Domain experience from defence, maritime, banking, government, retail and others.

The focus of my work is developing system integration/backend solutions for large and mission-critical systems.

From my base near Århus, Denmark, I contract for remote work in Scandinavia and Europe – with frequent traveling for workshops, team meetings, integration sessions, demos, etc.

### Open source projects

I am the inventor and maintainer of 

- [AISmessages](https://github.com/tbsalling/aismessages)
- [AISutils](https://github.com/tbsalling/aisutils)

Both projects have commercial users and are related to decoding safety- and navigation related digital messages from ships. The source code is available on Github and the binaries are in Maven Central.

### Posts

[What is AIS?](./blog_000_ais.html)

### Getting in touch

{% if site.data.social-media %}
<div id="social-media">
    {% assign sm = site.data.social-media %}
    {% for entry in sm %}
        {% assign key = entry | first %}
        {% if sm[key].enabled and sm[key].id %}
            <p><a href="{{ sm[key].href }}{{ sm[key].id }}" title="{{ sm[key].title }}"><i class="fa fa-brands {{ sm[key].fa-icon }}"></i> {{ sm[key].id }}</a></p>
        {% endif %}
    {% endfor %}
</div>
{% endif %}

### Technical keywords

Java SE, Java MP, Quarkus, Helm, OpenShift.

Java API's (JPA, Hibernate, CDI, JTA), databases (e.g. Oracle, Postgres, MS-SQL, Cassandra), build tools (Gradle, maven).
