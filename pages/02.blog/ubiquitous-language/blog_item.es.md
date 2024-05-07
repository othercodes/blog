---
title: 'Lenguaje Ubicuo'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
taxonomy:
    category:
        - blog
    tag:
        - 'arquitectura de software'
        - 'diseño guiado por el dominio'
        - ddd
date: '06-04-2020 16:00'
publish_date: '07-04-2020 16:00'
media_order: 'featured.jpg,ddd-lenguaje-ubicuo-01.png,ddd-lenguaje-ubicuo-02.png'
metadata:
    description: 'El lenguaje ubicuo define todos los conceptos de nuestro negocio, nos ayudará a comunicarnos correctamente y ante todo, nos ayudará a modelar correctamente el dominio.'
    keywords: 'ddd, lenguaje ubicuo, ubiquitous language, dominio, modelar dominio.'
slug: lenguaje-ubicuo
---

Una de las primeras cosas que se oye cuando se habla de DDD es el termino "_Lenguaje Ubicuo_". Es la base de todo en DDD, usando este lenguaje podremos definir todos los conceptos de nuestro negocio, nos ayudará a comunicarnos correctamente y ante todo, nos facilitará la tarea de modelar correctamente el dominio de nuestra solución.

===

Empecemos por definir "<a href="https://dle.rae.es/lenguaje" target="_blank" rel="nofollow">Lenguaje</a>":

> Facultad del ser humano de expresarse y comunicarse con los demás a través del sonido articulado o de otros sistemas de signos.

Bien, ahora definamos "<a href="https://dle.rae.es/ubicuo" target="_blank" rel="nofollow">Ubicuo</a>":

> Que está presente a un mismo tiempo en todas partes.

Dadas estas dos definiciones podemos asumir que un **Lenguaje Ubicuo es una forma de comunicación que se usa en todas partes**.

![Tom Cruise What?](https://media.giphy.com/media/glmRyiSI3v5E4/giphy.gif)

Quieto todo el mundo, que _no panda el cúnico_. ¿Partes? ¿Qué partes? Pues partes de nuestro **proyecto**, que bien podrían ser, **negocio** y **desarrollo** entre otras.

Como ya sabemos, todo negocio tiene su jerga, su manera de definir y nombrar las cosas. El Lenguaje Ubicuo busca integrar esta jerga de manera eficiente y fácil en todos los ambitos del proyecto. De manera que todos los integrantes de las partes interesadas que desarrollan el proyecto, llamen a las cosas por los mismos términos.

## Definiendo el Lenguaje Ubicuo

Supongamos que somos un estudio de software que va a desarrollar una aplicación para un cliente. Este, empieza a describir su negocio: "_Somos una empresa de logística de transportes, disponemos de varias flotas de vehículos de distintos modelos y características. Uno o varios vehículos o incluso la flota entera puede salir en misión, es decir, ser alquilada por nuestros clientes..._".

Como vemos, el cliente ha usado ciertos términos propios de su negocio, como por ejemplo, _vehículo_, _flota_, _modelo de vehículo_, _característica de vehículo_, _misión_.Todos estos términos **deben** formar parte de nuestro _lenguaje ubicuo_. Además de todos estos sustantivos, el cliente puede utilizar verbos que definan acciones u operaciones específicas de su negocio. Lo mas seguro es que los sustantivos acaben siendo _Entidades_ o <a href="index.php?option=com_content&view=article&id=26&catid=8">_Value Objects_</a> y los verbos acaben siendo _servicios de aplicación_ o _servicios de dominio_.

Veamos un ejemplo de como definir estas entiades usando UML:

![ddd-lenguaje-ubicuo-01](ddd-lenguaje-ubicuo-01.png "ddd-lenguaje-ubicuo-01")

Continuamos con la conversación, "_¿Cuáles son las características de una flota?_" le preguntamos, a lo que el cliente responde, "_Cada flota está identificada por una referencia única, además tiene un nombre y una base asociada._". Seguimos preguntando, "_¿Qué formato debería tener el identificador? y ¿qué es una 'base'?_". El cliente nos comenta "_El identificador de las flotas y de los vehículos es un identificador único universal. La base es la referencia del emplazamiento físico donde la flota está estacionada, también es un uuid._".

Actualicemos el esquema anterior:

![ddd-lenguaje-ubicuo-02](ddd-lenguaje-ubicuo-02.png "ddd-lenguaje-ubicuo-02")

Continuaremos con la conversación hasta que, tanto el cliente como nosotros, estemos satisfechos con el modelo inicial de la solución.

## Implementando el Lenguaje Ubicuo

El lenguaje ubicuo no solo se limita a la comunicación con nuestro cliente, tiene que formar parte intrinseca de la solución que estamos construyendo, por ejemplo:

```php
final class Fleet
{
	private $id;

	private $name;

	private $baseId;

	public function __construct(FleetId $id, FleetName $name, BaseId $baseId)
	{
		$this->id = $id;
		$this->name = $name;
		$this->baseId = $baseId;
	}
}

final class Vehicle
{
	private $id;

	private $fleetId;

	private $status;

	public function __construct(VehicleId $id, FleetId $fleetId, VehicleStatus $status)
	{
		$this->id = $id;
		$this->fleetId = $fleetId;
		$this->status = $status;
	}

	public function isInMission(): bool
	{
		return ($this->status === VehicleStatus::IN_MISSION);
	}
}
```

En este ejemplo vemos como el código de nuestra solución está llena de términos de nuestro lenguaje ubicuo. Hemos creado un metodo `isInMission` en la clase `Vehicle` que refleja directamente un concepto del modelo de negocio de nuestro cliente.

Es recomendable tener nuestro lenguaje ubicuo escrito y totalmente documentado, para que, en caso de duda podamos consultar cualquier término. Además, esto ayudará a los nuevos miembros del equipo a familiarizarse con el lenguaje ubicuo.

Como podemos ver, la manera de crear un lenguaje ubicuo es sencilla, solo debemos de conversar con nuestro cliente, asimilar la terminología de su negocio y aplicarla para modelar el dominio. Es muy importante definir correctamente un lenguaje ubicuo adecuado, ya que esto favorecerá una correcta comunicación entre nuestro cliente y nuestro equipo de desarrollo. Si nuestro cliente nos comenta que necesita cambios en la administración de las flotas, nosotros tenemos que saber al 100% a que se está refiriendo.

Finalmente, y a modo de resumen podemos decir que:

- El lenguaje ubicuo busca unificar la comunicación entre todas las partes interesadas del proyecto.
- Todos los integrantes de las partes interesadas en el proyecto deben usarlo con fluidez.
- Contiene términos propios del negocio para el cual se esta definiendo el lenguaje.
- Nos ayuda a modelar el dominio de nuestro negocio y trasladarlo facilmente a nuestra solución.
