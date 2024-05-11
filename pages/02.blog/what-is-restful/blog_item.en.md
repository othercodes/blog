---
title: 'What is RESTful?'
twitterenable: true
twittercardoptions: summary
articleenabled: false
facebookenable: true
taxonomy:
    category:
        - blog
    tag:
        - 'software architecture'
        - http
        - rest
media_order: featured.jpg
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
metadata:
    description: 'Introduction to the rest architecture, constraints, resources, methods and state codes.'
    'og:url': 'https://othercode.io/blog/what-is-restful'
    'og:type': article
    'og:title': 'What is RESTful? | otherCode'
    'og:description': 'Introduction to the rest architecture, constraints, resources, methods and state codes.'
    'og:image': 'https://othercode.io/user/pages/02.blog/what-is-restful/featured.jpg'
    'og:image:type': image/jpeg
    'og:image:width': 800
    'og:image:height': 350
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'What is RESTful? | otherCode'
    'twitter:description': 'Introduction to the rest architecture, constraints, resources, methods and state codes.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/what-is-restful/featured.jpg'
    'article:published_time': '2019-12-16T17:53:00+10:00'
    'article:modified_time': '2024-05-11T11:51:35+10:00'
    'article:author': otherCode
    keywords: 'rest, rest architecture, restrictions, resources, resource, methods, state code'
date: '16-12-2019 17:53'
publish_date: '16-12-2019 17:53'
aura:
    metadata:
        keywords: 'rest, rest architecture, restrictions, resources, resource, methods, state code'
    pagetype: article
    image: featured.jpg
    description: 'Introduction to the rest architecture, constraints, resources, methods and state codes.'
---

REST or Representational State Transfer is a type of architecture for hypermedia systems. This term was coined by [Roy Fielding](https://twitter.com/fielding) in his [doctoral thesis on the web](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) in 2000. Although the term REST originally refers to the architecture itself, it is currently used to describe any interface that uses HTTP to obtain data or initiate the execution of operations on data.

===

In recent years REST has established itself as the predominant design model for web services. We can find this type of API's in services offered by many well-known companies such as Amazon Web Services (AWS), Microsoft, Twitter or Facebook.

### Restrictions

Any strict RESTful system MUST adhere to certain constraints that define the ways in which the server can process and respond to client requests.

1. **Client-Server Architecture**: Separating user interface concerns from data processing logic provides portability as well as improving the scalability of system components. This in turn allows the system components to evolve independently, giving rise to so-called Microservices.
2. **Stateless**: Each communication between the client and the server contains all the information necessary to understand and process the request. As a result, neither the client nor the server needs to remember the state of the communications. This restriction is very important when working with transactional systems. It should be noted that many services use cookies or other mechanisms to save session state.
3. **Cache**: This restriction requires that the data in a response be tagged as cacheable or not. This allows the client to reuse the response data for future equivalent requests, which favors resource optimization when making requests to the server.
4. **Layered system**: A client typically cannot know whether it is connected directly to the end server or to an intermediary along the way. An example of an intermediary server could be a proxy server, a balancing system, or the authentication system.
5. **Uniform interface**: This constraint is fundamental for any RESTful system and requires four interface constraints:
	1. **Resource identification**: Each resource must be identified in a response or in a request. These resources are conceptually separate from the representations that are returned to the client.
	2. **Manipulation of resources through representations**: A representation of a resource must have enough information to modify or delete the same resource.
	3. **Self-descriptive messages**: The message itself must have the necessary information to describe how to process the message, for example, we can select one parser or another based on the mime type of the response.
	4. **Hypermedia as an engine of application state (HATEOAS)**: A REST client should be able to dynamically use the links provided by the server to learn about the possible actions and resources it offers.
6. **Code on demand (optional)**: Servers can temporarily extend or customize the functionality of a client by transferring executable code, for example, client-side scripts such as JavaScript.

If a system violates any of these restrictions, it cannot be considered RESTful.

### Resources

A very important concept in REST is that of a resource. Any nameable information can be a resource: a document or image, a temporary service, a collection of other resources, a non-virtual object, etc.

REST uses a unique identifier to identify a given resource in any interaction between components. To manipulate these resources, the network components (clients and servers) communicate through a standard interface (HTTP) exchanging the representations of said resources.

### Methods

Another important concept in REST is **method**. These methods are used to define the type of CRUD (Create, Read, Update and Delete) operation desired and are the HTTP verbs **GET/PUT/PATCH/POST/DELETE**.

As a curious fact, Roy Fielding never made any recommendations about which method should be used for which operation. He just pointed out that a REST system must have a common interface. That is, if you want to use **PUT** to create resources instead of **POST** there is no problem.

We commonly use the methods as follows:

| Método | Ruta         | Payload | Acción |  
| ---------- | ------------- | ----- | ---- |
| `GET`      | `/object` |  | Read |
| `GET`     | `/object/{id}` |   | Read |
| `POST`   | `/object` | Recurso | Create |
| `PUT`      | `/object/{id}` | Recurso | Update |
| `PATCH` | `/object/{id}` |  Recurso | Partial Update |
| `DELETE` | `/object/{id}` |  | Delete |

### Idempotent

Idempotence is the property of performing an action several times and still achieving the same result that would be obtained if it were performed only once. REST makes use of this property in some of its methods:

- POST is not idempotent.
- GET, PUT, DELETE, HEAD, OPTIONS and TRACE are idempotent.

### Response Codes

REST uses HTTP response codes to communicate to the client how the requested request went. There are a wide variety of HTTP codes divided into categories:

| Categoría | Descripción |
| ------------- | ------------- |
| 1xx:Informational | Communicates transfer protocol level information. | 
| 2xx: Success | Indicates that the client's request was successfully accepted. |
| 3xx: Redirection | Indicates that the customer must take some additional action to complete their request. |
| 4xx: Client Error | This category of error status codes signals customers. |
| 5xx: Server Error | The server is responsible for these error status codes. |

Let's look in detail at some of the code that applies to REST.

#### 200 (OK)

Indicates that the system successfully performed any action requested by the client. This code should always be accompanied by content that may vary depending on the action required.

#### 201 (Created)

This code is used every time a resource is created within a collection.

#### 202 (Accepted)

A response with this code indicates that the response has been accepted but is still in process. The returned entity MUST include indications of the current status of the request and some reference to a status monitor.

#### 204 (No Content)

Code 204 is generally used when the request has been processed successfully but the content has no representation, for example, when DELETE is used to delete a resource. The 204 response MUST not include a message body.

#### 301 (Moved Permanently)

This status indicates that the structure of the requested resource has been modified and has been permanently moved to a new URI.

#### 400 (Bad Request)

400 is the generic client-side error code. Used when no other 4xx error code is appropriate. Errors can be malformed request syntax, invalid request message parameters, or misleading request routing, etc.

#### 401 (Unauthorized)

A 401 error response indicates that the client attempted to operate on a protected resource without providing appropriate authorization. You may have provided the wrong credentials or none at all.

The client can repeat the request with an appropriate authorization header field.

#### 403 (Forbidden)

A 403 error response indicates that the client's request was formed correctly, but the system refuses to honor it.

Authentication will not help and the request should not be repeated.

#### 404 (Not Found)

The 404 error status code indicates that the system cannot map the client URI to a resource, but it may become available in the future. Subsequent customer requests are allowed.

#### 405 (Method Not Allowed)

 The system responds with a 405 error to indicate that the client attempted to use an HTTP method that the resource does not allow.

#### 415 (Unsupported Media Type)

The 415 error response indicates that the system cannot process the media type supplied by the client, as indicated by the content type request header.

#### 500 (Internal Server Error)

500 is the generic server-side error response.

#### 501 (Not Implemented)

The server does not recognize the request method or lacks the ability to fulfill the request.
