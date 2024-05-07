---
title: Agregados
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
date: '11-05-2020 17:27'
publish_date: '08-03-2021 06:23'
metadata:
    description: 'Uno de los conceptos más incomprendidos pero fundamentales en DDD son los agregados. Definirlos erróneamente puede traer consecuencias desastrosas para nuestros programas.'
    keywords: 'ddd, aggregates, agregados, dominio, modelar dominio, domain model, model'
taxonomy:
    category:
        - blog
    tag:
        - 'arquitectura de software'
        - 'diseño guiado por el dominio'
media_order: featured.jpg
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
slug: agregados
routes:
    aliases:
        - domain-driven-design-agregados
---

Modelar nuestros programas usando DDD (Domain-Driven Design) nos ayuda a trasladar la terminología, conceptos e ideas del dominio de nuestro negocio al software que estamos desarrollando. Los Agregados y las Entidades son elementos básicos a la hora de aplicar DDD. Uno de los conceptos más incomprendidos pero fundamentales en DDD son los agregados. Definirlos erróneamente puede traer consecuencias desastrosas para nuestros programas.

===

Empecemos por el principio, ¿qué es un agregado?, la definición formal de **Martin Fowler** es la siguiente: 

> A cluster of domain objects that can be treated as a single unit.

Perfecto, ahora sabemos que un agregado es un conjunto de objetos de dominio que pueden ser tratados como una sola unidad. Pero, ¿qué son los objetos de dominio?, esta es fácil, un objeto de dominio es cualquier objeto que define un concepto de nuestro dominio de negocio. 

Veamos un ejemplo. Supongamos que queremos modelar motocicletas, para nosotros un objeto de dominio podría ser el `Motor` o una `Rueda` de nuestra motocicleta. Como ya habréis imaginado, el objeto `Motocicleta` en sí, sería nuestro agregado. Esto nos permite definir ciertas invariantes de nuestro modelo como, por ejemplo: 

- Una motocicleta debe tener exactamente dos ruedas. 
- Una motocicleta debe tener un motor. 
- El motor puede ser diesel o gasolina. 

Hasta aquí todo más o menos claro, sigamos con la definición de Fowler. 

> Any references from outside the aggregate should only go to the aggregate root. The root can thus ensure the integrity of the aggregate as a whole. 

Cualquier referencia de fuera del agregado solo debe ir a la raíz del agregado, es decir, todas las interacciones desde fuera de nuestro objeto `Motocicleta` deben realizarse contra el objeto `Motocicleta`. Por ejemplo, si queremos comunicarnos con el objeto `Motor` deberemos hacerlo usando el objeto `Motocicleta` como intermediario. Bajo ninguna circunstancia podremos acceder directamente al `Motor`. El agregado actúa como interfaz de los demás objetos de dominio, exponiendo el comportamiento permitido para los objetos internos (Ley de Demeter). Esto permite trabajar con la `Motocicleta` como un "todo" en vez de tratar a los objetos `Motocicleta` y `Motor` como entidades aisladas, manteniendo así la integridad del conjunto de objetos. Por ejemplo, si nuestro agregado `Motocicleta` no expone un método para desmontar una `Rueda`, sería imposible hacerlo, lo que garantizaría que nuestra `Motocicleta` **siempre** tendrá dos ruedas. 

> Aggregates are the basic element of transfer of data storage - you request to load or save whole aggregates. Transactions should not cross aggregate boundaries. 

Los agregados en todo su conjunto son los elementos que se almacenan en el sistema de persistencia. Cuando queremos cargar o guardar un dato, debemos usar el agregado completo. Es decir, cuando queremos persistir nuestro objeto `Motocicleta` lo haremos persistiendo el objeto entero, jamás persistiremos las ruedas, el motor y los demás objetos de la motocicleta de manera separada. 

### Conclusión

Los agregados modelan los conceptos principales de nuestro dominio, salvaguarda la integridad de los demás objetos directamente relacionados con él y sirve de interfaz a la hora de interactuar con ellos. Son uno de los pilares del DDD, definiéndolos correctamente podemos modelar y encapsular la lógica de nuestro negocio de forma efectiva.