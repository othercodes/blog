---
title: 'Value Objects'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - 'software architecture'
        - 'domain-driven design'
        - ddd
        - 'design patterns'
        - php
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
date: '23-03-2020 14:50'
publish_date: '23-03-2020 14:50'
metadata:
    'og:url': 'https://othercode.io/blog/value-objects'
    'og:type': article
    'og:title': 'Value Objects | otherCode'
    'og:image': 'https://othercode.io/user/pages/02.blog/value-objects/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Value Objects | otherCode'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/value-objects/featured.jpg'
    'article:published_time': '2020-03-23T14:50:00+10:00'
    'article:modified_time': '2024-05-11T11:50:27+10:00'
    'article:author': otherCode
    description: 'Value Objects (VO) are a fundamental piece of Domain-Driven-Design (DDD) and object-oriented programming.'
    keywords: 'ddd, php, value objects, immutability, design patterns'
aura:
    metadata:
        description: 'Value Objects (VO) are a fundamental piece of Domain-Driven-Design (DDD) and object-oriented programming.'
        keywords: 'ddd, php, value objects, immutability, design patterns'
    pagetype: article
    image: featured.jpg
---

**Value Objects (VO)** are a fundamental piece of Domain-Driven-Design (DDD) and object-oriented programming. They describe a concept of our domain layer.

===

[Martin Fowler](https://martinfowler.com/eaaCatalog/valueObject.html) defines them as: 

> *A small simple object, like money or a date range, whose equality isn't based on identity.* 

The main characteristics of a VO are:

- Describe a concept.
- It is validated in construction.
- It is compared by value, not by identity.
- It is immutable.

## Describe a concept

A VO describes a concept in our domain layer whether quantifiable or not, for example, a point on a coordinate axis, temperature, or a customer's address.

## Construction validation

Every VO must be able to self-validate during construction, this ensures that once the object is built it is valid. As good practice a VO should only be composed of primitive values and other VOs. For example, at a point on a coordinate axis, the values ​​of X and Y can only be values of primitive type `float`, it does not make sense for

```php
final class Point
{
    private $x;
    
    private $y;

    public function __construct(float $x, float $y)
    {
        $this->x = $x;
        $this->y = $y;
    }
}
```

## Identity vs value

What does it mean that a VO **is compared by value, not by identity**? Well, in VO, as its name indicates, what matters is its value. Let's look at a small example:

```php
final class Point
{
    private $x;

    private $y;

    public function __construct(float $x, float $y)
    {
        $this->x = $x;
        $this->y = $y;
    }
    
    public function getX(): float
    {
        return $this->x;
    }

    public function getY(): float
    {
        return $this->y;
    }
}

$p1 = new Point(2, 2);
$p2 = new Point(2, 2);
```

In the example above we have two variables `$p1` and `$p2`, both instances of `Point` and both with the same X and Y coordinates. At first glance we can say that technically the objects are the same, let's compare them:

```php
var_dump($d1 == $d2);
bool(true)
```

The result is `true`, everything is correct, both objects have the same value so they are equal, but what happens if we now use `===` to perform the comparison?

```php
var_dump($d1 === $d2);
bool(false)
```

This time the result is `false` since the identity of these objects is not based on their value, but is defined by a unique identifier or reference that makes these objects different.

> In PHP, the `==` operator is used to evaluate equality, while the `===` operator is used to evaluate identity. To facilitate the evaluation of two VOs we can add an `isEqual()` method to the API of the `Point` class:

```php
final class Point
{
    private $x;

    private $y;

    public function __construct(float $x, float $y)
    {
        $this->x = $x;
        $this->y = $y;
    }

    public function getX(): float
    {
        return $this->x;
    }

    public function getY(): float
    {
        return $this->y;
    }

    public function isEqual(Point $point): bool
    {
        return ($this->x == $point->getX() && $this->y == $point->getY());
    }
}

$p1 = new Point(2, 2);
$p2 = new Point(2, 2);
$p3 = new Point(2, 4);

var_dump($p1->isEqual($p2));
bool(true);
    
var_dump($p1->isEqual($p3));
bool(false);
```

## Immutability

Another fundamental aspect of VOs is that they are immutable, this means that once defined they cannot be altered. Since VOs are intended to be small and lightweight, every time a VO has to be altered, another one is created with the new values ​​in its place.

For example, if we wanted to change the X value of point `$p1` we would define a *setter* that would allow us to change that value. This is not allowed in VO since they are immutable, instead we can have the *setter* return a new instance of the class with the new value:

```php
final class Point
{
    private $x;
    
    private $y;

    public function __construct(float $x, float $y)
    {
        $this->x = $x;
        $this->y = $y;
    }
    
    public function getX(): float
    {
        return $this->x;
    }

    public function getY(): float
    {
        return $this->y;aa
    }
    
    public function setX(float $x): Point
    {
        return new self($x, $this->y);
    }

    public function setY(float $y): Point
    {
        return new self($this->x, $y);
    }
    
    public function isEqual(Point $point): bool
    {
        return ($this->x == $point->getX() && $this->y == $point->getY());
    }
}

$p1 = new Point(2, 2);
$p1 = $p1->setX(4);
```

## Benefits

Knowing the characteristics of VOs we can say that they offer us the following benefits:

- Facilitates reusability due to the encapsulation of domain logic.
- We avoid repetitive validations throughout our application, the VO is responsible for self-validation.
- By being immutable we avoid unwanted alterations throughout the life cycle.

## Related Books

- [Domain-Driven Design in PHP ](https://amzn.to/3digE8W)
- [Domain-Driven Design: Tackling Complexity in the Heart of Software](https://amzn.to/3cqJyT2)
