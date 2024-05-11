---
title: Aggregates
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
date: '11-05-2020 17:27'
publish_date: '08-03-2021 06:23'
taxonomy:
    category:
        - blog
    tag:
        - 'software architecture'
        - 'domain-driven design'
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
metadata:
    'og:url': 'https://othercode.io/blog/aggregates'
    'og:type': website
    'og:title': 'Aggregates | otherCode'
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Aggregates | otherCode'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'article:published_time': '2021-03-08T06:23:00+10:00'
    'article:modified_time': '2024-05-11T11:38:08+10:00'
    'article:author': otherCode
    description: 'One of the most misunderstood but fundamental concepts in DDD is aggregates. Defining them incorrectly can have disastrous consequences for our programs.'
    keywords: 'ddd, aggregates, aggregates, domain, domain model, domain model, model'
aura:
    metadata:
        description: 'One of the most misunderstood but fundamental concepts in DDD is aggregates. Defining them incorrectly can have disastrous consequences for our programs.'
        keywords: 'ddd, aggregates, aggregates, domain, domain model, domain model, model'
    pagetype: website
---

Modeling our programs using DDD (Domain-Driven Design) helps us transfer the terminology, concepts and ideas of our business domain to the software we are developing. Aggregates and Entities are basic elements when applying DDD. One of the most misunderstood but fundamental concepts in DDD is aggregates. Defining them incorrectly can have disastrous consequences for our programs.

===

Let's start at the beginning, what is an aggregate? The formal definition by **Martin Fowler** is as follows:

> A cluster of domain objects that can be treated as a single unit.

Perfect, now we know that an aggregate is a set of domain objects that can be treated as a single unit. But what are domain objects? This is easy, a domain object is any object that defines a concept of our business domain.

Let's look at an example. Suppose we want to model motorcycles, for us a domain object could be the `Engine` or a `Wheel` of our motorcycle. As you may have already imagined, the 'Motorcycle' object itself would be our aggregate. This allows us to define certain invariants of our model, such as:

- A motorcycle must have exactly two wheels.
- A motorcycle must have an engine.
- The engine can be diesel or gasoline.

So far everything is more or less clear, let's continue with Fowler's definition.

> Any references from outside the aggregate should only go to the aggregate root. The root can thus ensure the integrity of the aggregate as a whole.

Any references from outside the aggregate should only go to the root of the aggregate, i.e. all interactions from outside our `Motorcycle` object should be against the `Motorcycle` object. For example, if we want to communicate with the `Motor` object we must do so using the `Motorcycle` object as an intermediary. Under no circumstances will we be able to directly access the 'Engine'. The aggregate acts as an interface to the other domain objects, exposing the behavior allowed for internal objects (Law of Demeter). This allows you to work with the `Motorcycle` as a "whole" instead of treating the `Motorcycle` and `Engine` objects as isolated entities, thus maintaining the integrity of the set of objects. For example, if our `Motorcycle` aggregate does not expose a method to remove a `Wheel`, it would be impossible to do so, which would guarantee that our `Motorcycle` will **always** have two wheels.

> Aggregates are the basic element of transfer of data storage - you request to load or save whole aggregates. Transactions should not cross aggregate boundaries.

Aggregates as a whole are the elements that are stored in the persistence system. When we want to load or save a data, we must use the full aggregate. That is, when we want to persist our 'Motorcycle' object we will do so by persisting the entire object, we will never persist the wheels, the engine and the other objects of the motorcycle separately.

### Conclusion

Aggregates model the main concepts of our domain, safeguard the integrity of other objects directly related to it and serve as an interface when interacting with them. They are one of the pillars of DDD, by defining them correctly we can model and encapsulate the logic of our business effectively.