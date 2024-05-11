---
title: '¿Que es RESTful?'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - 'arquitectura de software'
        - http
        - rest
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
metadata:
    description: 'Introducción a la arquitectura rest, restricciones, recursos, métodos y códigos de estado.'
    'og:url': 'https://othercode.io/es/blog/que-es-restful'
    'og:type': article
    'og:title': '¿Que es RESTful? | otherCode'
    'og:description': 'Introducción a la arquitectura rest, restricciones, recursos, métodos y códigos de estado.'
    'og:image': 'https://othercode.io/user/pages/02.blog/what-is-restful/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': '¿Que es RESTful? | otherCode'
    'twitter:description': 'Introducción a la arquitectura rest, restricciones, recursos, métodos y códigos de estado.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/what-is-restful/featured.jpg'
    'article:published_time': '2019-12-16T17:53:00+10:00'
    'article:modified_time': '2024-05-11T11:51:10+10:00'
    'article:author': otherCode
    keywords: 'rest, arquitectura rest, restircciones, recursos, recurso, metodos, codigo de estado'
date: '16-12-2019 17:53'
publish_date: '16-12-2019 17:53'
slug: que-es-restful
aura:
    metadata:
        keywords: 'rest, arquitectura rest, restircciones, recursos, recurso, metodos, codigo de estado'
    pagetype: article
    image: featured.jpg
    description: 'Introducción a la arquitectura rest, restricciones, recursos, métodos y códigos de estado.'
---

REST o Transferencia de estado representacional es un tipo de arquitectura para sistemas hipermedia. Este término fue acuñado por [Roy Fielding](https://twitter.com/fielding) en su [tesis doctoral sobre la web](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) en el año 2000. Aunque el término REST se refiere originalmente a la arquitectura en sí, actualmente es utilizado para describir cualquier interfaz que utiliza HTTP para obtener datos o iniciar la ejecución de operaciones sobre datos.

===

En los últimos años REST se ha establecido como el modelo de diseño predominante para servicios web. Podemos encontrar este tipo de API's en servicios ofrecidos por muchas empresas muy conocidas tales como Amazon Web Services (AWS), Microsoft, Twitter o Facebook.

### Restricciones

Todo sistema RESTful estricto DEBE cumplir ciertas restricciones que definen las formas en las que el servidor puede procesar y responder las solicitudes del cliente.

1. **Arquitectura Cliente-Servidor**: Separar las preocupaciones de la interfaz de usuario de la lógica de proceso de datos ofrece portabilidad además de mejorar la escalabilidad de los componentes del sistema. Esto a su vez permite que los componentes del sistema evolucionen de forma independiente dando lugar a los llamados Microservicios.
2. **Sin estado**: Cada comunicación entre el cliente y el servidor contiene toda la información necesaria para comprender y procesar la petición. Como resultado ni el cliente ni el servidor necesitan recordar el estado de las comunicaciones. Esta restricción es muy importante al trabajar con sistemas transaccionales. Cabe destacar que muchos servicios usan cookies u otros mecanismos para guardar el estado de la sesión.  
3. **Caché**: Esta restricción requiere que los datos de una respuesta estén etiquetados como cacheables o no. Esto permite al cliente reutilizar los datos de respuesta para futuras solicitudes equivalentes, lo que favorece la optimización de recursos a la hora de realizar peticiones al servidor.
4. **Sistema por capas**: Un cliente normalmente no puede saber si está conectado directamente al servidor final o a un intermediario en el camino. Un ejemplo de servidor intermediario podría ser un servidor proxy, un sistema balanceador o el sistema de autenticación.
5. **Interfaz uniforme**: Esta restricción es fundamental para cualquier sistema RESTful y requiere de cuatro restricciones de interfaz:
	1. **Identificación de recursos**: Cada recurso debe de estar identificado en una respuesta o en una solicitud. Estos recursos están conceptualmente separados de las representaciones que se devuelven al cliente.
	2. **Manipulación de recursos a través de representaciones**: Una representación de un recurso debe tener la suficiente información para modificar o eliminar el mismo recurso.
	3. **Mensajes autodescriptivos**: El mensaje en sí debe tener la información necesaria para describir cómo procesar el mensaje, por ejemplo, podemos seleccionar un parser u otro en base al mime type de la response.
	4. **Hipermedia como motor del estado de la aplicación (HATEOAS)**: Un cliente REST debería poder usar dinámicamente los enlaces proporcionados por el servidor para conocer las posibles acciones y recursos que este ofrece. 
6. **Code on demand (opcional)**: Los servidores pueden ampliar o personalizar temporalmente la funcionalidad de un cliente mediante la transferencia de código ejecutable, por ejemplo, scripts del lado del cliente, como JavaScript.

Si un sistema viola alguna de estas restricciones, no puede considerarse RESTful.  

### Recursos

Un concepto muy importante en REST es el de recurso. Cualquier información que se pueda nombrar puede ser un recurso: un documento o imagen, un servicio temporal, una colección de otros recursos, un objeto no virtual, etc.

REST utiliza un identificador único para identificar un recurso dado en cualquier interacción entre componentes. Para manipular estos recursos, los componentes de la red (clientes y servidores), se comunican a través de una interfaz estándar (HTTP) intercambiando las representaciones de dichos recursos.

### Métodos

Otro concepto importante en REST es el de **método**. Estos métodos son usados para definir el tipo de operación CRUD (Create, Read, Update y Delete) deseada y son los verbos HTTP **GET/PUT/PATCH/POST/DELETE**.

Como dato curioso Roy Fielding nunca expuso ninguna recomendación sobre qué método debe ser usado para que operación. Solo señaló que un sistema REST debe tener una interfaz común. Es decir, si quieres usar **PUT** para crear recursos en vez de **POST** no hay problema.

Comúnmente usamos los métodos de la siguiente manera:

| Método | Ruta         | Payload | Acción |  
| ---------- | ------------- | ----- | ---- |
| `GET`      | `/objeto` |  | Read |
| `GET`     | `/objeto/{id}` |   | Read |
| `POST`   | `/objeto` | Recurso | Create |
| `PUT`      | `/objeto/{id}` | Recurso | Update |
| `PATCH` | `/objeto/{id}` |  Recurso | Partial Update |
| `DELETE` | `/objeto/{id}` |  | Delete |

### Idempotente

La idempotencia es la propiedad para realizar una acción varias veces y aun así conseguir el mismo resultado que se obtendría si se realizase una sola vez. REST hace uso de esta propiedad en algunos de sus métodos:

1. POST no es idempotente. 
2. GET, PUT, DELETE, HEAD, OPTIONS y TRACE si son idempotentes.

### Códigos de Respuesta

REST usa los códigos de respuesta HTTP para comunicar al cliente cómo ha ido la petición solicitada. Existen una gran variedad de códigos HTTP divididos en categorías:

| Categoría | Descripción |
| ------------- | ------------- |
| 1xx:Informational | Comunica la información de nivel de protocolo de transferencia. | 
| 2xx: Success | Indica que la solicitud del cliente fue aceptada con éxito. |
| 3xx: Redirection | Indica que el cliente debe tomar alguna acción adicional para completar su solicitud. |
| 4xx: Client Error | Esta categoría de códigos de estado de error señala a los clientes. |
| 5xx: Server Error | El servidor se responsabiliza por estos códigos de estado de error. |

Veamos en detalle algunos de los códigos que se aplican a REST.

#### 200 (OK)

Indica que el sistema realizó con éxito cualquier acción solicitada por el cliente. Este código siempre debería ir acompañado de un contenido que puede variar dependiendo de la acción requerida.

#### 201 (Created)

Este código es usado cada vez que se crea un recurso dentro de una colección.

#### 202 (Accepted)

Una respuesta con este código indica que la respuesta ha sido aceptada pero aún está en proceso. La entidad devuelta DEBE incluir indicaciones del estado actual de la solicitud y alguna referencia a un monitor de estado.

#### 204 (No Content)

El código 204 generalmente es usado cuando la solicitud se ha procesado correctamente pero el contenido no tiene ninguna representación, por ejemplo, cuando se usa DELETE para borrar un recurso. La respuesta 204 no DEBE incluir un cuerpo de mensaje.

#### 301 (Moved Permanently)

Este estado indica que la estructura del recurso requerido ha sido modificada y que se ha movido permanentemente a una nueva URI.

#### 400 (Bad Request)

400 es el código de error genérico del lado del cliente. Se utiliza cuando ningún otro código de error 4xx es apropiado. Los errores pueden ser sintaxis de solicitud con formato incorrecto, parámetros de mensaje de solicitud no válidos o enrutamiento de solicitud engañoso, etc.

#### 401 (Unauthorized)

Una respuesta de error 401 indica que el cliente intentó operar en un recurso protegido sin proporcionar la autorización adecuada. Puede haber proporcionado las credenciales incorrectas o ninguna. 

El cliente puede repetir la solicitud con un campo de encabezado de autorización adecuado.

#### 403 (Forbidden)

Una respuesta de error 403 indica que la solicitud del cliente se formó correctamente, pero el sistema se niega a cumplirla.

La autenticación no ayudará y la solicitud no debe repetirse.

#### 404 (Not Found)

El código de estado de error 404 indica que el sistema no puede asignar el URI del cliente a un recurso, pero puede estar disponible en el futuro. Se permiten solicitudes posteriores del cliente.

#### 405 (Method Not Allowed)

 El sistema responde con un error 405 para indicar que el cliente intentó utilizar un método HTTP que el recurso no permite.

#### 415 (Unsupported Media Type)

La respuesta de error 415 indica que el sistema no puede procesar el tipo de medio suministrado por el cliente, como lo indica el encabezado de solicitud de tipo de contenido.

#### 500 (Internal Server Error)

500 es la respuesta genérica de error de lado de servidor.

#### 501 (Not Implemented)

El servidor no reconoce el método de solicitud o carece de la capacidad para cumplir con la solicitud.
