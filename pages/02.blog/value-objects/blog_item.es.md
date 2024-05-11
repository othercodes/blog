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
        - 'arquitectura de software'
        - 'diseño guiado por el dominio'
        - ddd
        - 'patrones de diseño'
        - php
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
media_order: featured.jpg
date: '23-03-2020 14:50'
publish_date: '23-03-2020 14:50'
metadata:
    description: 'Los Value Objects (VO) son una pieza fundamental del diseño dirigido por dominio (Domain-Driven-Design o DDD) y de la programación orientada a objetos. '
    'og:url': 'https://othercode.io/es/blog/value-objects'
    'og:type': article
    'og:title': 'Value Objects | otherCode'
    'og:description': 'Los Value Objects (VO) son una pieza fundamental del diseño dirigido por dominio (Domain-Driven-Design o DDD) y de la programación orientada a objetos. '
    'og:image': 'https://othercode.io/user/pages/02.blog/value-objects/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Value Objects | otherCode'
    'twitter:description': 'Los Value Objects (VO) son una pieza fundamental del diseño dirigido por dominio (Domain-Driven-Design o DDD) y de la programación orientada a objetos. '
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/value-objects/featured.jpg'
    'article:published_time': '2020-03-23T14:50:00+10:00'
    'article:modified_time': '2024-05-11T11:50:17+10:00'
    'article:author': otherCode
    keywords: 'ddd, php, value objects, inmutabilidad, patrones de diseño'
aura:
    metadata:
        keywords: 'ddd, php, value objects, inmutabilidad, patrones de diseño'
    pagetype: article
    image: featured.jpg
    description: 'Los Value Objects (VO) son una pieza fundamental del diseño dirigido por dominio (Domain-Driven-Design o DDD) y de la programación orientada a objetos. '
---

Los **Value Objects (VO)** son una pieza fundamental del diseño dirigido por dominio (Domain-Driven-Design o DDD) y de la programación orientada a objetos. Describen un concepto de nuestra capa de dominio.

===

[Martin Fowler](https://martinfowler.com/eaaCatalog/valueObject.html) los define como: 

> *A small simple object, like money or a date range, whose equality isn't based on identity.* 

En español: 

> *Un objeto pequeño simple, como el dinero o una fecha, cuya igualdad no se basa en la identidad.* 

Las principales características de un VO son:

- Describe un concepto. 
- Se valida en la construcción. 
- Se compara por valor, no por identidad. 
- Es inmutable. 

## Describe un concepto 

Un VO describe un concepto de nuestra capa de dominio ya sea cuantificable o no, por ejemplo, un punto de un eje de coordenadas, la temperatura o la dirección de un cliente. 

## Validación en la construcción 

Todo VO tiene que poder auto validarse durante la construcción, esto nos asegura que una vez construido el objecto es válido. Como buena práctica un VO solo debería esta compuesto por valores primitivos y otros VO. Por ejemplo, en un punto de un eje de coordenadas, los valores de X e Y solo pueden ser valores de tipo primitivo `float`, no tiene sentido que X o Y sean de tipo `string` o `booleano`.

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

## Identidad vs valor 

¿Que significa que un VO **se compara por valor, no por identidad**? Bien, en los VO, tal y como indica su nombre, lo que importa es su valor. Veamos un pequeño ejemplo:

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

En el ejemplo de arriba tenemos dos variables `$p1` y `$p2`, ambas instancias de `Point` y ambas con las mismas coordenadas X e Y. A primera vista podemos decir que técnicamente los objetos son iguales, vamos a compararlos:

```php
var_dump($d1 == $d2);
bool(true)
```

El resultado es `true`, todo correcto, ambos objetos tienen el mismo valor así que son iguales pero, ¿qué pasa si ahora usamos `===` para realizar la comparación?

```php
var_dump($d1 === $d2);
bool(false)
```

Esta vez el resultado es `false` ya que la identidad de estos objetos no está basada en su valor, sino que viene definida por un identificador o referencia única que hace que estos objetos sean diferentes. 
    
> En PHP, el operador `==` se usa para evaluar la igualdad, mientras que el `===` para evaluar la identidad. Para facilitar la evaluación de dos VO podemos añadir un método `isEqual()` a el API de la clase `Point`:

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

## Inmutabilidad 

Otro aspecto fundamental de los VO es que son inmutables, esto quiere decir que una vez definidos no pueden ser alterados. Ya que los VO están pensados para que sean pequeños y ligeros, cada vez que un VO tiene que ser alterado, se crea otro con los nuevos valores en su lugar. 

Por ejemplo, si quisiéramos cambiar el valor X del punto `$p1` definiríamos un *setter* que nos permitiese cambiar ese valor. Esto no esta permitido en VO ya que son inmutables, en su lugar, podemos hacer que el *setter* devuelva una instancia nueva de la clase con el nuevo valor:

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

## Beneficios 

Sabiendo las características de los VO podemos decir que nos ofrecen los siguientes beneficios: 

- Facilita la reusabilidad debido a la encapsulación de la lógica de dominio. 
- Evitamos validaciones repetitivas a lo largo de nuestra aplicación, el VO se encarga de auto validarse. 
- Al ser inmutable evitamos las alteraciones no deseadas a lo largo del ciclo de vida. 

## Libros Relacionados 

- [Domain-Driven Design in PHP ](https://amzn.to/3digE8W)
- [Domain-Driven Design: Tackling Complexity in the Heart of Software](https://amzn.to/3cqJyT2)


