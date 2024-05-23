---
title: 'Testeando Servicios en Arquitectura Hexagonal'
aura:
    pagetype: article
    description: 'Optimiza la prueba de tus servicios en la Arquitectura Hexagonal utilizando el mock de interfaces y la validación de contratos. Aprende cómo simplificar los procesos de prueba sin configuraciones de infraestructura complejas.'
    metadata:
        keywords: 'pruebas de servicios en arquitectura hexagonal, pruebas de servicios, arquitectura hexagonal, contratos de interfaz, simulación de interfaz, desarrollo de software, pruebas unitarias, simulación de repositorio, simulación de comportamiento'
    image: featured.webp
metadata:
    description: 'Optimiza la prueba de tus servicios en la Arquitectura Hexagonal utilizando el mock de interfaces y la validación de contratos. Aprende cómo simplificar los procesos de prueba sin configuraciones de infraestructura complejas.'
    'og:url': 'https://othercode.io/es/blog/testeando-servicios-en-arquitectura-hexagonal'
    'og:type': article
    'og:title': 'Testeando Servicios en Arquitectura Hexagonal | otherCode'
    'og:description': 'Optimiza la prueba de tus servicios en la Arquitectura Hexagonal utilizando el mock de interfaces y la validación de contratos. Aprende cómo simplificar los procesos de prueba sin configuraciones de infraestructura complejas.'
    'og:image': 'https://othercode.io/user/pages/02.blog/testing-services-in-hexagonal-architecture/featured.webp'
    'og:image:type': image/webp
    'og:image:width': 900
    'og:image:height': 300
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Testeando Servicios en Arquitectura Hexagonal | otherCode'
    'twitter:description': 'Optimiza la prueba de tus servicios en la Arquitectura Hexagonal utilizando el mock de interfaces y la validación de contratos. Aprende cómo simplificar los procesos de prueba sin configuraciones de infraestructura complejas.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/testing-services-in-hexagonal-architecture/featured.webp'
    'article:published_time': '2024-05-23T10:55:00+10:00'
    'article:modified_time': '2024-05-23T11:07:06+10:00'
    'article:author': otherCode
    keywords: 'pruebas de servicios en arquitectura hexagonal, pruebas de servicios, arquitectura hexagonal, contratos de interfaz, simulación de interfaz, desarrollo de software, pruebas unitarias, simulación de repositorio, simulación de comportamiento'
date: '23-05-2024 10:55'
taxonomy:
    category:
        - blog
    tag:
        - 'arquitectura de software'
        - php
        - 'arquitectura hexagonal'
        - 'prubas unitarias'
media_order: featured.webp
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
slug: testeando-servicios-en-arquitectura-hexagonal
---

En la arquitectura de software, asegurar una comunicación efectiva entre los diferentes componentes de un sistema es vital. Una técnica efectiva para verificar estas conexiones es mediante la creación de mocks de interfaces. Esto implica crear varias implementaciones de las interfaces para imitar el comportamiento de los diferentes componentes. Al usar estos mocks, podemos probar los servicios de manera independiente, asegurándonos de que los mensajes que envían y reciben se adhieran a un estándar predefinido, comúnmente conocido como "contrato". Este enfoque nos permite validar las interacciones y la funcionalidad de cada servicio sin necesidad de las implementaciones o infraestructuras reales.

=== 

En la [Arquitectura Hexagonal](/blog/arquitectura-hexagonal), seguimos la práctica de crear una interfaz o contrato distinto para cada servicio que pueda tener una o más implementaciones. Estas implementaciones se asignan luego a diferentes capas. Por ejemplo, consideremos un contrato `UserRepository` que puede tener múltiples implementaciones como `SQLiteUserRepository`, `PostGreSQLUserRepository` o `JSONFSUserRepository`, dependiendo de las necesidades de escalabilidad de nuestro sistema. La interfaz o contrato pertenece a la capa de dominio o aplicación, mientras que las implementaciones reales residen en la capa de infraestructura.

Al emplear mocks de interfaces, simplificamos el proceso de prueba de las capas de dominio y aplicación sin la complejidad de configurar un entorno de infraestructura real.

Supongamos que tenemos el siguiente servicio de aplicación del artículo anterior sobre [Arquitectura Hexagonal](/blog/arquitectura-hexagonal):

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

El servicio anterior depende de un contrato `UserRepository` que expone los siguientes métodos:

```php
interface UserRepository  
{  
    public function find(int $id): ?User;

	public function all(): array

    public function save(User $user): User;
}
```

Inicialmente, al probar el servicio `UserFinder`, uno podría asumir que es necesario tener una base de datos funcional. Esto implicaría desplegar el esquema, cargar la base de datos con datos de prueba, crear una instancia de una implementación del servicio `UserRepository`, y luego inyectarla en la nueva instancia de `UserFinder` para la prueba.

Sin embargo, este proceso puede ser demasiado complejo para probar solo un servicio de aplicación. Afortunadamente, podemos simplificarlo realizando mocks de la interfaz `UserRepository`. Este enfoque nos permite probar el código en las capas de dominio y aplicación sin la necesidad de ningún servicio de infraestructura real.

Para asegurar una prueba adecuada de los servicios de dominio y aplicación, debemos validar rigurosamente la API expuesta por el contrato que estamos probando. Esto significa probar exhaustivamente todas las entradas y salidas especificadas por el contrato.

Exploremos un ejemplo rápido usando los frameworks [Pest](https://pestphp.com/) y [Mockery](https://github.com/mockery/mockery). Primero, necesitamos instalar ambos frameworks a través de composer:

```bash
composer require pestphp/pest --dev --with-all-dependencies
composer require mockery/mockery --dev
```

A continuación, necesitamos inicializar Pest:

```bash
vendor/bin/pest --init
```

Ahora, creemos nuestra primera prueba unitaria en `tests/Unit/UserFinderTest.php`. El primer caso de uso que probaremos es encontrar un usuario por su ID único, lo que implica probar la función `byId`. Esta función llama al método `find` del contrato `UserRepository`. Según el contrato, este método puede devolver una instancia válida de `User` (éxito) o null si no se encuentra un usuario con el ID especificado (fallo). Con esto en mente, escribamos la prueba.

```php
describe('UserFinder', function () {

	test('should find a `User` by give ID.', function () {  
	    $repository = Mockery::mock(UserRepository::class);  
	    $repository->expects('find')  
	        ->with(Mockery::type('int')) 
	        ->once()  
	        ->andReturnUsing(fn(int $id) => new User($id, 'Vincent Vega'));  
	  
	    $service = new UserFinder($repository);  
	  
	    expect($service->byId(42))->toBeInstanceOf(User::class);  
	});

});
```

Vamos a revisar lo que hemos hecho:

1. Creamos un mock del contrato `UserRepository`.
2. Especificamos que el mock debe esperar una llamada al método `find` con un argumento de tipo `int`.
3. Definimos que el método `find` debe ser llamado exactamente una vez durante esta prueba.
4. Proporcionamos una función que se ejecutará cuando se llame al método `find`. Esta función devuelve una nueva instancia de `User`, que coincide con el resultado esperado definido por el contrato para un caso exitoso.
5. Instanciamos un nuevo `UserFinder`, nuestro sujeto de prueba.
6. Ejecutamos el método `byId` de `UserFinder` y verificamos la respuesta. Dado que estamos probando el escenario exitoso, el valor devuelto debe ser una instancia de `User`.

Dado que nuestro sujeto de prueba es el `UserFinder`, debemos verificar lo que este servicio expone. Al mockear el `UserRepository`, podemos controlar el comportamiento de `UserFinder` durante la prueba. En esta primera prueba, queremos que el `UserRepository` siempre devuelva una instancia de `User`, simulando un escenario donde existe un registro de usuario coincidente en una base de datos. Como podemos ver, no necesitamos una base de datos en funcionamiento para ejecutar la prueba; solo necesitamos adherirnos correctamente al contrato definido.

Ya hemos probado el caso de éxito, así que ahora probemos el caso de fallo. Según el contrato `UserRepository`, si no se encuentra un usuario con el ID dado, el valor devuelto debe ser `null`.

```php
describe('UserFinder', function () {  

	//...

    test('should throw `UserNotFound` exception when a `User` is not found by the given ID.', function () {  
		$repository = Mockery::mock(UserRepository::class);  
		$repository->expects('find')  
		    ->with(Mockery::type('int'))  
		    ->once()  
		    ->andReturnUsing(fn(int $id) => null);  
		  
		$service = new UserFinder($repository);  
		$service->byId(42); 
    })->throws(UserNotFound::class, "User 42 not found");

});
```

Esta nueva prueba tiene algunas diferencias; vamos a revisar los cambios:

1. El valor devuelto de la función ahora es `null`, simulando que la base de datos no tiene el registro de usuario solicitado.
2. En lugar de verificar directamente el valor devuelto del servicio, verificamos que se debe lanzar una excepción. Específicamente, esperamos una excepción `UserNotFound`.

Este caso de prueba verifica el escenario de fallo del `UserFinder`. Al cambiar el comportamiento del `UserRepository` y adherirse al contrato, podemos probar toda la funcionalidad sin ninguna implementación real de un `UserRepository`.

Vamos a continuar añadiendo más pruebas a nuestro `UserFinder`. Ahora es el momento de cubrir el método `all`. Este método llama al método `all` del `UserRepository`. Según el contrato, este método siempre devuelve un array.

```php
describe('UserFinder', function () {

	// ...
  
    test('should return all `User`s', function () {  
		$repository = Mockery::mock(UserRepository::class);  
		$repository->expects('all')  
		    ->withNoArgs()  
		    ->once()  
		    ->andReturnUsing(fn() => []);  
		  
		$service = new UserFinder($repository);  
		  
		expect($service->all())->toBeArray();
    });});
```

En este caso, indicamos al mock que:

1. No espere ningún argumento para el método `all`.
2. Devuelva un array vacío cuando se llame al método `all`.
3. Finalmente, afirmamos que el valor devuelto por el `UserFinder` es realmente un array.

En este punto, hemos probado exhaustivamente el servicio `UserFinder`. A continuación, pasemos al servicio `UserCreator`, que también depende del `UserRepository`. Para este servicio, necesitamos probar el método `save`. Este método toma un `User` sin un ID como argumento y devuelve un `User` con un ID.

```php
describe('UserCreator', function () {  

    test('should create a new `User` from the given data.', function () {  
        $repository = Mockery::mock(UserRepository::class);  
        $repository->expects('save')  
            ->with(Mockery::type(User::class))  
            ->once()  
            ->andReturnUsing(fn(User $user) => new User(1, $user->name));  
  
        $creator = new UserCreator($repository);  
        $user = $creator->create('Vincent Vega');  
  
        expect($user->name)->toBe('Vincent Vega')  
            ->and($user->id())->toBeGreaterThan(0);  
    });  

});
```

En el código anterior, hicimos lo siguiente:

1. Creamos un simulacro del contrato `UserRepository`.
2. Especificamos que el simulacro debe esperar una llamada al método `save` con un argumento `User` y que debe devolver un `User`.
3. Definimos que el método `save` debe ser llamado exactamente una vez durante el caso de prueba.
4. Aseguramos que el `User` devuelto tenga un ID `int` válido.
5. Instanciamos el `UserCreator`.
6. Ejecutamos el método `save` y afirmamos la respuesta. Dado que estamos probando el escenario exitoso, el valor devuelto debe ser una instancia de `User` con un ID `int` válido.

## Conclusión

El uso de mocks de interfaz en la Arquitectura Hexagonal ayuda a garantizar que las diferentes partes de tu aplicación funcionen correctamente sin necesidad de una configuración compleja. Al enfocarnos en los contratos o interfaces, podemos probar la lógica central sin configurar una infraestructura real como bases de datos.

En este artículo, mostramos cómo usar los mocks de interfaz para verificar los servicios `UserFinder` y `UserCreator`. Al crear mocks del `UserRepository`, controlamos cómo se comportaban estos servicios durante las pruebas. Esto nos permitió verificar que respondieran correctamente en diferentes situaciones sin necesidad de una base de datos real.