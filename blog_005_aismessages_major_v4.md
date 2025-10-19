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

AISmessages 4.0.0 (Java 21) introduces immutable value objects for all AIS message classes and moves parsing out of the data model. See the v4.0.0 notes for details: [AISmessages Release Notes – 4.0.0](https://github.com/tbsalling/aismessages/blob/63cf0dd54e5fa5ecffb5daa23fc9bc1ff7da7fc1/RELEASE_NOTES.md#version-400).

## Why change mutability and datatypes?

- Correct value semantics:
  - True immutability across message types.
  - Enables safe use in sets, maps, de-duplication, and caching.
- Thread-safety by design:
  - No defensive copying or locks; instances are freely shareable across threads.
- Fewer bugs, simpler reasoning:
  - No state changes after construction; easier testing and debugging.
- Clear separation of concerns:
  - Message classes are pure data carriers; parsing is handled by dedicated utilities (e.g., `BitStringParser`).

## What changed concretely in v4

- Immutability:
  - All AIS message classes are immutable value objects (Lombok `@Value`).
  - `equals`/`hashCode` support added for all message types.
- Architecture:
  - Parsing responsibilities removed from value objects and centralized in parsing utilities.
  - Cleaner boundary between NMEA handling and AIS decoding.
- Datatypes:
  - Fields audited for consistent, intention-revealing, stable types that work well with value semantics (no accidental mutation exposure).
- Tooling:
  - Lombok added as a compile-time–only dependency (zero runtime deps preserved).

## Impact for users

- Treat message instances as read-only. No need to worry about defensive copying or locks.
- If prior code mutated message fields, refactor to construct new instances or adjust flow to use the parser outputs.
- Benefit from stable equality and hashing when using collections or caches.

## Motivation summary

- “AIS message classes are now immutable value objects using Lombok @Value”
- “Message objects are now pure data carriers with no parsing responsibilities”
- “Improved thread-safety and reduced mutation-related bugs”
- “Introduced BitStringParser class to separate parsing concerns from value objects”
- “Added Lombok … as provided dependency for compile-time code generation”

## Further reading

- [AISmessages Release Notes – 4.0.0](https://github.com/tbsalling/aismessages/blob/63cf0dd54e5fa5ecffb5daa23fc9bc1ff7da7fc1/RELEASE_NOTES.md#version-400)
