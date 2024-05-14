---
title: 'Arquitectura Hexagonal'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
media_order: featured.jpg
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
published: false
access:
    admin: true
metadata:
    'og:url': 'https://othercode.io/es/blog/arquitectura-hexagonal'
    'og:type': article
    'og:title': 'Arquitectura Hexagonal | otherCode'
    'og:image': 'https://othercode.io/user/pages/02.blog/hexagonal-architecture/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Arquitectura Hexagonal | otherCode'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/hexagonal-architecture/featured.jpg'
    'article:published_time': '2024-05-11T11:44:16+10:00'
    'article:modified_time': '2024-05-14T20:25:22+10:00'
    'article:author': otherCode
    keywords: ''
taxonomy:
    category:
        - blog
    tag:
        - 'arquitectura de software'
        - 'arquitectura hexagonal'
        - 'puertos y adaptadores'
        - 'arquitecturas limpias'
slug: arquitectura-hexagonal
aura:
    metadata:
        keywords: ''
    pagetype: article
    image: featured.jpg
---


La Arquitectura Hexagonal, también conocida como Arquitectura de Puertos y Adaptadores, divide un sistema en múltiples componentes intercambiables y con [bajo acoplamiento estructural](https://en.wikipedia.org/wiki/Loose_coupling). A diferencia de la arquitectura tradicional en capas, la Arquitectura Hexagonal enfatiza un enfoque más flexible y modular.

===

> La palabra "hexagonal" puede llevar a malinterpretaciones, ya que no se refiere a un número finito ("seis") de elementos.

En esta arquitectura, cada componente pertenece a una capa específica y está conectado a otras capas a través de "puertos" o "contratos". Estos puertos o contratos facilitan la comunicación siguiendo protocolos específicos basados en su propósito previsto. Los puertos y protocolos definen una API abstracta que puede implementarse utilizando diversas tecnologías, lo que permite la adaptabilidad y facilidad de integración con sistemas externos.

## Capas

El sistema está organizado en tres capas distintas, cada una con un propósito específico, encapsulando diferentes aspectos del software.

![Layers](hexagonal-architecture-layers.png)

**Capa de Dominio**

Incluye los conceptos dentro de nuestro contexto (Cliente, Producto, Orden, Suscripción, Usuario, etc.) y las reglas de negocio que son exclusivas de nuestra aplicación.

**Capa de Aplicación**

Es donde se encuentran los casos de uso de nuestra aplicación (compra de productos, creación de nuevos productos, registro de clientes, etc.).

**Capa de Infraestructura**

Contiene el código que cambia en función de decisiones externas (SDK, frameworks, bibliotecas, etc.). En esta capa encontramos las implementaciones (adaptadores) de las interfaces (puertos) que definiremos en el nivel de dominio y aplicación.

## Regla de Dependencia

El código que se encuentra en cada una de nuestras capas debe conocer solo las clases ubicadas en la capa inmediatamente siguiente. Entendemos el orden de las capas de afuera hacia adentro:

> **Infraestructura -> Aplicación -> Dominio**

Esta regla proporciona la posibilidad de cambiar elementos de nuestras capas exteriores sin afectar las internas. Por esta razón, tiene más sentido que los aspectos con mayor variabilidad estén en la capa más externa (infraestructura), ya que no dependen de nosotros.

> Esta regla también se conoce como el DIP o [Principio de Inversión de Dependencia](https://en.wikipedia.org/wiki/Dependency_inversion_principle), uno de los Principios SOLID.

## Beneficios

Ahora, exploremos algunos de los beneficios que ofrece esta arquitectura:

1. **Modularidad**: La Arquitectura Hexagonal promueve un diseño modular al separar claramente las responsabilidades en capas. Esto mejora la comprensión, mantenimiento y extensibilidad del sistema.

2. **Testabilidad**: La clara delimitación de responsabilidades en la Arquitectura Hexagonal simplifica las pruebas unitarias. Al aislar la lógica central del negocio de las dependencias externas como bases de datos o marcos de UI, escribir pruebas unitarias y de integración se vuelve más sencillo.

3. **Flexibilidad**: La Arquitectura Hexagonal aporta flexibilidad en la selección e intercambio de implementaciones. A través de la definición de interfaces o contratos (puertos) para dependencias externas, como bases de datos o APIs externas, diferentes implementaciones pueden integrarse fácilmente sin afectar la lógica central de la aplicación.

4. **Bajo Acoplamiento**: Al establecer un límite claro entre el núcleo de la aplicación y sus dependencias externas, la arquitectura minimiza el acoplamiento entre componentes. Esto mejora la mantenibilidad del sistema y reduce la probabilidad de efectos secundarios no deseados al implementar cambios.

5. **Migración Simplificada**: Con la Arquitectura Hexagonal, la transición a nuevas tecnologías o frameworks se simplifica. Dado que la lógica central del negocio está protegida de las dependencias externas, las modificaciones a los componentes de infraestructura pueden realizarse sin interrumpir la lógica de la aplicación.

6. **Escalabilidad**: La estructura modular de la Arquitectura Hexagonal facilita la escalabilidad de las aplicaciones a medida que se expanden. Se pueden incorporar nuevas características sin modificaciones extensas al código existente, ademas de que se pueden introducir implementaciones adicionales para gestionar una carga de trabajo mas grande.

## Como implementar Arquitectura Hexagonal

Supongamos que estamos desarrollando una aplicación fundamental de gestión de usuarios. Dentro de este sistema, implementaremos un `UserCreateController` y un `UserListController` para gestionar las solicitudes de CLI asociadas con los usuarios. Además, utilizaremos un `UserRepository` para facilitar las interacciones con la base de datos.

**Definimos las Entidades del Dominio**

La entidad `User` es bastante simple, definimos un identificador único y el nombre de usuario. Esto pertenece a la capa de Dominio.

```php
final class User  
{  
    public function __construct(  
        private ?int $id,  
        public string $name  
    ) {  
    }
    
    public function id(): ?int  
    {  
        return $this->id;  
    }  
    
    public static function create(string $name): self  
    {  
        return new self(null, $name);  
    }
}
```

**Definimos el Contrato (Interfaz) para el Servicio de Infraestructura (Externo)**

El contrato del repositorio tiene tres métodos: `find`, que nos permite obtener un único `User` por ID; el método `save`, que persiste un `User` en el sistema de persistencia; y `all`, que nos permite obtener todos los `User` del sistema de persistencia. Este contrato pertenece a la capa de dominio o aplicación.

```php
interface UserRepository  
{  
    public function find(int $id): ?User;

	public function all(): array

    public function save(User $user): User;
}
```

**Implementamos los Servicios de Aplicación**

El servicio de búsqueda es responsable de localizar al usuario requerido por su ID. Si el usuario no se encuentra, este servicio generará una excepción de dominio llamada `UserNotFound`. Dado que esta es una implementación de una funcionalidad de negocio, la ubicamos dentro de la capa de aplicación.

```php
final readonly class UserFinder  
{  
    public function __construct(  
        private UserRepository $repository  
    ) {  
    }  
    
    public function byId(int $id): User  
    {  
        if (!$user = $this->repository->find($id)) {  
            throw new UserNotFound("User $id not found");  
        }  
        return $user;  
    }
	
	public function all(): array
	{
		return $this->repository->all();
	} 
}
```

El creador es responsable de recopilar los datos entrantes e instanciar un nuevo Usuario. Posteriormente, llama al repositorio designado para persistir al usuario. Si hubiera un bus de eventos disponible para publicar eventos, este sería el lugar adecuado para invocarlo.

```php
final class UserCreator
{
    public function __construct(
        private UserRepository $repository
    ) {
    }

    public function create(string $name): User
    {
        return $this->repository->save(User::create($name));
    }
}
```

**Crea las Implementaciones del Contrato**

Ahora es el momento de implementar los servicios de infraestructura reales. Para esta implementación del repositorio usaremos [Doctrine](https://symfony.com/doc/current/doctrine.html).

```php
readonly class DoctrineUserRepository implements UserRepository  
{  
    public function __construct(  
        private EntityManagerInterface $entityManager  
    ) {  
    }
      
    public function find(int $id): ?User  
    {  
        return $this->entityManager->find(User::class, $id);  
    }
     
    public function save(User $user): User  
    {  
        $this->entityManager->persist($user);  
        $this->entityManager->flush();  
  
        return $user;
    }
    
    public function delete(User $user): void  
    {  
        $this->entityManager->remove($user);  
        $this->entityManager->flush();  
    }
}
```

Al utilizar Doctrine, cubrimos todas las bases de datos soportadas por esta biblioteca.

En este ejemplo, `UserFinder` y `UserRepository` se encuentran en la capa de aplicación, mientras que las diversas implementaciones de `UserRepository` representan la capa de infraestructura.

**Creamos los Controladores**

Finalmente, es momento de implementar los controladores de la aplicación.

```php
#!/usr/bin/env php  
<?php

declare(strict_types=1);  
  
require_once "vendor/autoload.php";  
  
use OtherCode\UserManagement\Application\UserCreator;  
use OtherCode\UserManagement\Infrastructure\DoctrineUserRepository;  
  
$repository = new DoctrineUserRepository(require __DIR__.'/../database.php');  
$service = new UserCreator($repository);
  
if (!isset($argv[1])) {  
    echo "Please input name.\n";  
    exit(1);  
}  
  
$user = $service->create($argv[1]);  
  
echo "New user $user->name (#{$user->id()}) created!\n";  
exit(0);
```

El `UserCreateController` y el `UserListController` exponen la lógica empresarial a través de una aplicación de linea de comandos, pero pueden ser reemplazados fácilmente, por ejemplo, con un controlador HTTP. Aunque este componente pertenece a la infraestructura, al constituir la "Aplicación" _per se_ (no la Capa de Aplicación), podemos organizar el código según las convenciones del framework que se esté utilizando. 

Nótese que en este ejemplo no hemos usado ningún framework, ya que el propósito era ilustrar cómo se dividen los componentes en las diferentes capas, no aprender a implementar arquitectura Hexagonal con un framework en particular.

## Conclusión

En resumen, la Arquitectura Hexagonal proporciona una forma flexible de diseñar sistemas de software. Divide el sistema en componentes separados, que son fáciles de reemplazar e interactuar entre sí. Estos componentes están organizados en capas: Dominio, Aplicación e Infraestructura, lo que facilita la comprensión y el mantenimiento del sistema. La Regla de Dependencia garantiza que los cambios en una capa no afecten a otras, lo que la hace adaptable y fácil de modificar. En general, la Arquitectura Hexagonal es un patrón valioso para el desarrollo de software moderno, ofreciendo una mayor flexibilidad y escalabilidad.