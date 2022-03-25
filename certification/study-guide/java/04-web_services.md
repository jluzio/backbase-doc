# RESTful Web Services
Representational state transfer (REST) is a software architectural style that was created to guide the design and development of the architecture for the World Wide Web. REST defines a set of constraints for how the architecture of an Internet-scale distributed hypermedia system, such as the Web, should behave. The REST architectural style emphasises the scalability of interactions between components, uniform interfaces, independent deployment of components, and the creation of a layered architecture to facilitate caching components to reduce user-perceived latency, enforce security, and encapsulate legacy systems.[1]

REST has been employed throughout the software industry and is a widely accepted set of guidelines for creating stateless, reliable web APIs. A web API that obeys the REST constraints is informally described as RESTful. RESTful web APIs are typically loosely based on HTTP methods to access resources via URL-encoded parameters and the use of JSON or XML to transmit data.

"Web resources" were first defined on the World Wide Web as documents or files identified by their URLs. Today, the definition is much more generic and abstract, and includes every thing, entity, or action that can be identified, named, addressed, handled, or performed in any way on the Web. In a RESTful Web service, requests made to a resource's URI elicit a response with a payload formatted in HTML, XML, JSON, or some other format. For example, the response can confirm that the resource state has been changed. The response can also include hypertext links to related resources. The most common protocol for these requests and responses is HTTP. It provides operations (HTTP methods) such as GET, POST, PUT, and DELETE.[2] By using a stateless protocol and standard operations, RESTful systems aim for fast performance, reliability, and the ability to grow by reusing components that can be managed and updated without affecting the system as a whole, even while it is running.

The goal of REST is to increase performance, scalability, simplicity, modifiability, visibility, portability, and reliability. This is achieved through following REST principles such as a client–server architecture, statelessness, cacheability, use of a layered system, support for code on demand, and using a uniform interface. These principles must be followed for the system to be classified as RESTful.

## Architectural constraints

### Client–server architecture
See also: Client–server model

The client-server design pattern enforces the principle of separation of concerns: separating the user interface concerns from the data storage concerns. Portability of the user interface is thus improved. In the case of the Web, a plethora of web browsers have been developed for most platforms without the need for knowledge of any server implementations. Separation also simplifies the server components, improving scalability, but more importantly it allows components to evolve independently (anarchic scalability), which is necessary in an Internet-scale environment that involves multiple organisational domains.

### Statelessness
See also: Stateless protocol

In computing, a stateless protocol is a communications protocol in which no session information is retained by the receiver, usually a server. Relevant session data is sent to the receiver by the client in such a way that every packet of information transferred can be understood in isolation, without context information from previous packets in the session. This property of stateless protocols makes them ideal in high volume applications, increasing performance by removing server load caused by retention of session information.

### Cacheability
See also: Web cache

As on the World Wide Web, clients and intermediaries can cache responses. Responses must, implicitly or explicitly, define themselves as either cacheable or non-cacheable to prevent clients from providing stale or inappropriate data in response to further requests. Well-managed caching partially or completely eliminates some client–server interactions, further improving scalability and performance.

### Layered system
See also: Layered system

A client cannot ordinarily tell whether it is connected directly to the end server or to an intermediary along the way. If a proxy or load balancer is placed between the client and server, it won't affect their communications, and there won't be a need to update the client or server code. Intermediary servers can improve system scalability by enabling load balancing and by providing shared caches. Also, security can be added as a layer on top of the web services, separating business logic from security logic. Adding security as a separate layer enforces security policies. Finally, intermediary servers can call multiple other servers to generate a response to the client.

### Code on demand (optional)
See also: Client-side scripting

Servers can temporarily extend or customize the functionality of a client by transferring executable code: for example, compiled components such as Java applets, or client-side scripts such as JavaScript.


## Semantics of HTTP methods

The following table shows how HTTP methods are intended to be used in HTTP APIs, including RESTful ones.
Semantics of HTTP methods HTTP method 	Description
- GET: Get a representation of the target resource’s state.
- POST: Let the target resource process the representation enclosed in the - request P  	Create or replace the state of the target resource with the state - defined tresentation enclosed in the request.
- DELETE: Delete the target resource’s state.

The GET method is safe, meaning that applying it to a resource does not result in a state change of the resource (read-only semantics).

The GET, PUT, and DELETE methods are idempotent, meaning that applying them multiple times to a resource results in the same state change of the resource as applying them once, though the response might differ.

The GET and POST methods are cacheable, meaning that responses to them are allowed to be stored for future reuse.

```java
// Example JAX-RS
@Path("/notifications")
public class NotificationsResource {
  @GET
  @Path("/ping")
  public Response ping() {
    return Response.ok().entity("Service online").build();
  }

  @GET
  @Path("/get/{id}")
  @Produces(MediaType.APPLICATION_JSON)
  public Response getNotification(@PathParam("id") int id) {
    return Response.ok()
        .entity(new Notification(id, "john", "test notification"))
        .build();
  }

  @POST
  @Path("/post/")
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces(MediaType.APPLICATION_JSON)
  public Response postNotification(Notification notification) {
    return Response.status(201).entity(notification).build();
  }
}
```

```java
// Example Spring
@RestController
public class GreetingController {

  @GetMapping(path = "/webmvc/hello")
  public String hello() {
    return "Hello, WebMvc Spring!";
  }

}
```