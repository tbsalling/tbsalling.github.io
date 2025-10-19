---
layout: post
title: "Why AISmessages 4.0 made value objects immutable"
description: "Rationale behind datatype and mutability changes in AISmessages v4.0.0"
date: 2025-10-19 07:35:26 +0000
tags: [AISmessages, Java, Immutability, Design]
categories: AIS
---

{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

# Immutable value objects in AISmessages v4
_(Posted {{page.date}})_

AISmessages 4.0.0 (Java 21) introduces immutable value objects for all AIS message classes and moves parsing out of the data model. See the v4.0.0 notes for details: [AISmessages Release Notes – 4.0.0](https://github.com/tbsalling/aismessages/blob/63cf0dd54e5fa5ecffb5daa23fc9bc1ff7da7fc1/RELEASE_NOTES.md#version-400).

## Why change mutability and datatypes?

The move to immutable value objects brings several important benefits:

**Correct value semantics:**
- True immutability across message types.
- Enables safe use in sets, maps, de-duplication, and caching.

**Thread-safety by design:**
- No defensive copying or locks; instances are freely shareable across threads.

**Fewer bugs, simpler reasoning:**
- No state changes after construction; easier testing and debugging.

**Clear separation of concerns:**
- Message classes are pure data carriers; parsing is handled by dedicated utilities (e.g., `BitStringParser`).

## What changed concretely in v4

**Immutability:**
- All AIS message classes are immutable value objects (Lombok `@Value`).
- `equals`/`hashCode` support added for all message types.

**Architecture:**
- Parsing responsibilities removed from value objects and centralized in parsing utilities.
- Cleaner boundary between NMEA handling and AIS decoding.

**Datatypes:**
- Fields audited for consistent, intention-revealing, stable types that work well with value semantics (no accidental mutation exposure).

**Tooling:**
- Lombok added as a compile-time–only dependency (zero runtime deps preserved).

## Impact for users

When upgrading to AISmessages v4, keep the following in mind:

- Treat message instances as read-only. No need to worry about defensive copying or locks.
- If prior code mutated message fields, refactor to construct new instances or adjust flow to use the parser outputs.
- Benefit from stable equality and hashing when using collections or caches.

## Example: Using immutable AIS messages

Here's a simple example showing how to work with the new immutable value objects:

{% highlight java %}
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

// Parse NMEA message to get immutable AIS message
AISMessage message = NMEAMessage.fromString(
    "!AIVDM,1,1,,A,18UG;P0012G?Uq4EdHa=c;7@051@,0*53"
).getAISMessage();

// Safe to use in collections - equals/hashCode work correctly
Set<AISMessage> messageSet = new HashSet<>();
messageSet.add(message);

// Thread-safe - can be safely shared across threads
// No defensive copying needed
ExecutorService executor = Executors.newFixedThreadPool(4);
executor.submit(() -> processMessage(message));

// Values are accessible but cannot be modified
int mmsi = message.getSourceMmsi().getMMSI();
double lat = ((PositionReport) message).getLatitude();
{% endhighlight %}

## Motivation summary

The key changes in AISmessages v4.0.0:

{% highlight text %}
• AIS message classes are now immutable value objects using Lombok @Value
• Message objects are now pure data carriers with no parsing responsibilities
• Improved thread-safety and reduced mutation-related bugs
• Introduced BitStringParser class to separate parsing concerns from value objects
• Added Lombok as provided dependency for compile-time code generation
{% endhighlight %}

## Migration example

In v3, message objects were mutable and parsing was embedded in the objects themselves:

{% highlight java %}
// OLD v3 approach - messages were mutable
AISMessage message = parser.parse(nmea);
// Note: In v3, the API was different and the architecture mixed
// parsing logic with data representation
{% endhighlight %}

In v4, messages are immutable value objects with separated parsing:

{% highlight java %}
// NEW v4 approach - immutable value objects
AISMessage message = parser.parse(nmea);
// All fields are final - cannot be modified after creation
// equals() and hashCode() work correctly for collections
// No need for defensive copying in multi-threaded code
{% endhighlight %}

## Benefits in practice

Here's how the immutability benefits you in real-world scenarios:

{% highlight java %}
// Thread-safe caching without locks
public class AISMessageCache {
    private final Map<Integer, AISMessage> cache = new ConcurrentHashMap<>();
    
    public void cache(AISMessage message) {
        // Safe - no defensive copy needed
        cache.put(message.getSourceMmsi().getMMSI(), message);
    }
    
    public AISMessage get(int mmsi) {
        // Safe - returned message cannot be modified
        return cache.get(mmsi);
    }
}

// De-duplication using Sets
Set<AISMessage> uniqueMessages = new HashSet<>();
uniqueMessages.add(message1);
uniqueMessages.add(message2);
// equals/hashCode work correctly for deduplication
{% endhighlight %}

## Further reading

- [AISmessages Release Notes – 4.0.0](https://github.com/tbsalling/aismessages/blob/63cf0dd54e5fa5ecffb5daa23fc9bc1ff7da7fc1/RELEASE_NOTES.md#version-400)
