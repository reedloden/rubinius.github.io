---
layout: doc_en
title: Garbage Collector
previous: Object layout
previous_url: memory-system/object-layout
next: Rubinius Systems
next_url: systems
review: true
---

## Introduction

Rubinius implements a [Generational Garbage Collector (GC)][gc].
The Rubinius Generational GC manages the dynamic allocation and deallocation in
the heap space of a running Rubinius process.

## Nursery

The first generation in Rubinius' generational GC is the nursery.
An object's lifecycle begins in the nursery where it is created (allocated).

Objects live in the nursery until the very next collection. If an object
is still alive after one GC cycle then it is moved to the Young generation.
A run of the GC handles moving objects out of the Nursery automatically.

## Young generation

Objects that have been alive for more than one GC cycle live in the Young
generation space.  A run of the GC handles moving objects into the Young
generation space automatically.

Objects live in the young generation space for X number of collections.
X is dynamically adjusted based on how many objects are being promoted.

If *too many* objects are promoted during a given GC cycle, then X goes up.
If *not enough* objects are promoted during a given GC cycle, then X goes down.

This is done in order to keep the rate of promotion stable.
*stable* in this context means a low rate of change in object promotion.

## Mature generation

Mature objects are objects that have been promoted from the Young
generation after living past the promotion threshold 'X'.

Autotune is the mechanism that is used to dynamically adjust the GC cycles before
mature collection occurs. This can be turned off or a static number may be used
via `gc.lifetime`. `gc.lifetime` is used by autotune which sets the initial value.

For more information on configuration variables available read the
[vm configuration source file ][config]

## Large objects

Large objects are born in a birthing tank rather than in the nursery.

Any object that exceeds the defined byte size for a large object upon
allocation is allocated directly into the large objects space.  This is done to
avoid copying since copying large objects is an expensive operation.

Note that you can tune the number of bytes that constitute a *large
object* which by default is 2700 (such a good baud rate eh?) bytes.

    rbx ... -Xgc.large_object=<number of bytes> ...

For more information on configuration variables available read the
[vm configuration source file ][config]

[gc]: http://en.wikipedia.org/wiki/Garbage_collection_(computer_science)#Generational_GC_.28ephemeral_GC.29
[config]: https://github.com/rubinius/rubinius/blob/master/library/rubinius/configuration.rb
