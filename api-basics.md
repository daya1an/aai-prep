# API Programming — AAI Exam Notes

API programming enables independent software systems—such as an airline booking system, airport operations platform, mobile app, and payment gateway—to exchange data and functionality securely and reliably. The main API styles are **REST**, **SOAP**, and **GraphQL**; REST uses resources and HTTP, SOAP uses XML-based messaging, and GraphQL lets clients request exactly the data they need. [generalistprogrammer](https://generalistprogrammer.com/tutorials/restful-api-design-complete-guide)

## 1. API Fundamentals

An **API (Application Programming Interface)** is a contract that defines how two software components communicate. It exposes operations and data without revealing internal implementation.

**Airport analogy:** A passenger uses the airline’s website or mobile app to book a flight. The app does not directly access the airline’s internal reservation database; it calls an API such as `POST /flights/bookings`. The API validates the request, creates the booking, and returns a confirmation.

### Key terms

| Term | Meaning | Airport/airline example |
|---|---|---|
| Client | System that sends a request | Airline mobile app |
| Server | System that processes a request | Airline booking service |
| Request | Client asks the server for an action/data | Search flights from Chennai to Delhi |
| Response | Server returns result/status | Available flights and fares |
| Endpoint | URL/path representing an API operation | `/api/v1/flights/search` |
| Resource | Business entity exposed by API | Flight, passenger, booking, airport |
| Payload | Data sent/received in request/response | Passenger name, travel date, seat class |
| Authentication | Verifies who is calling | Airline partner API key or OAuth token |
| Authorization | Checks what caller can do | Travel agent can book but not cancel all bookings |
| Rate limiting | Restricts number of requests | Prevents excessive flight-search calls |
| Idempotency | Same request repeated gives same result | Retrying booking-status lookup is safe |
| Versioning | Manages API changes | `/v1/flights`, `/v2/flights` |
| API gateway | Single entry point managing APIs | Routes, secures, and limits airline APIs |
| SDK | Prebuilt library for consuming an API | Airline Java/Python SDK |

### Why APIs matter

- Enable **modular development**: one team builds the flight-search API while another builds the mobile app.
- Support **microservices**: booking, payment, baggage, check-in, and loyalty services can evolve independently.
- Enable **platform independence**: web, Android, iOS, airport kiosks, and partner systems can consume the same API.
- Improve **scalability**: services can be scaled independently based on demand.
- Enable **integration**: airlines, airports, travel agencies, payment providers, and government systems work together.

## 2. REST APIs

**REST** stands for **Representational State Transfer**. A REST API exposes resources through URLs and manipulates them using standard HTTP methods; JSON is the most common response format. [generalistprogrammer](https://generalistprogrammer.com/tutorials/restful-api-design-complete-guide)

### REST principles

| Principle | Meaning | Airline example |
|---|---|---|
| Client–server | Client and server are independent | Mobile app and booking service |
| Stateless | Each request contains all needed information | Every booking request includes token and booking data |
| Cacheable | Responses can be cached when allowed | Airport list can be cached |
| Uniform interface | Standard resource-oriented operations | `/flights`, `/bookings`, `/airports` |
| Layered system | Intermediate layers may exist | API gateway, load balancer, services |
| Code on demand | Optional: server can send executable code | Rarely used |

### HTTP methods

| Method | Purpose | Idempotent | Safe | Airline example |
|---|---|---:|---:|---|
| GET | Read data | Yes | Yes | Get flight status |
| POST | Create resource or trigger action | No | No | Create a booking |
| PUT | Replace a resource fully | Yes | No | Replace full passenger profile |
| PATCH | Partially update a resource | Not guaranteed | No | Update seat preference |
| DELETE | Remove a resource | Yes | No | Cancel a booking |
| HEAD | Get headers only | Yes | Yes | Check flight endpoint availability |
| OPTIONS | Get supported methods | Yes | Yes | Discover CORS-supported operations |

GET and HEAD are retrieval methods and should not modify server state. [restfulapi](https://restfulapi.net/http-methods/)

### REST endpoint design

Use **nouns for resources** and HTTP methods for actions.

| Good endpoint | Method | Purpose |
|---|---|---|
| `/api/v1/flights` | GET | List/search flights |
| `/api/v1/flights/{flightId}` | GET | Get one flight |
| `/api/v1/flights/{flightId}/status` | GET | Get flight status |
| `/api/v1/bookings` | POST | Create booking |
| `/api/v1/bookings/{bookingId}` | GET | Get booking |
| `/api/v1/bookings/{bookingId}` | PATCH | Update booking |
| `/api/v1/bookings/{bookingId}/cancel` | POST | Cancel booking |
| `/api/v1/airports/{iataCode}` | GET | Get airport details |
| `/api/v1/passengers/{passengerId}/bags` | GET | Get baggage details |

Avoid verb-based endpoints such as `/getFlights` or `/createBooking`; use `/flights` with `GET` and `/bookings` with `POST`.

### HTTP status codes

| Code | Meaning | Airline example |
|---:|---|---|
| 200 OK | Successful request | Flight status returned |
| 201 Created | Resource created | Booking created |
| 204 No Content | Success with no response body | Booking deleted |
| 400 Bad Request | Invalid client input | Missing travel date |
| 401 Unauthorized | Missing/invalid credentials | Expired access token |
| 403 Forbidden | Authenticated but not allowed | Agent cannot cancel another airline’s booking |
| 404 Not Found | Resource does not exist | Invalid booking ID |
| 409 Conflict | Request conflicts with current state | Seat already booked |
| 422 Unprocessable Entity | Semantically invalid input | Departure date before today |
| 429 Too Many Requests | Rate limit exceeded | Too many flight searches |
| 500 Internal Server Error | Server-side failure | Booking database failure |
| 503 Service Unavailable | Service temporarily unavailable | Check-in service down |

### REST request/response example

**Request:**

```http
GET /api/v1/flights?from=MAA&to=DEL&date=2026-11-15 HTTP/1.1
Host: api.airline.com
Authorization: Bearer <token>
Accept: application/json
```

**Response:**

```json
{
  "flights": [
    {
      "flightId": "AI-202",
      "airline": "Air India",
      "from": "MAA",
      "to": "DEL",
      "departureTime": "2026-11-15T08:30:00+05:30",
      "arrivalTime": "2026-11-15T11:25:00+05:30",
      "fare": 7500,
      "currency": "INR",
      "seatsAvailable": 12
    }
  ],
  "totalCount": 1
}
```

### REST constraints for MCQs

- REST is an **architectural style**, not a protocol.
- REST commonly uses **HTTP** and **JSON**.
- REST is **stateless**.
- `GET` is safe and idempotent.
- `POST` is neither safe nor idempotent.
- `PUT` is idempotent; `PATCH` is not guaranteed idempotent.
- `DELETE` is idempotent.
- REST resources should be represented by **nouns**, not verbs.
- **OpenAPI/Swagger** is commonly used to document REST APIs.
- REST APIs are usually easier to consume than SOAP for web and mobile clients.

## 3. SOAP APIs

**SOAP** stands for **Simple Object Access Protocol**. It is an XML-based messaging protocol for exchanging structured information between systems, commonly over HTTP. [en.wikipedia](https://en.wikipedia.org/wiki/SOAP)

### SOAP message structure

A SOAP message has three main parts:

| Part | Purpose |
|---|---|
| Envelope | Mandatory root element defining the SOAP message |
| Header | Optional metadata: authentication, transaction, routing |
| Body | Mandatory application data/request/response |
| Fault | Optional error information inside the body |

SOAP uses XML for its message format and commonly uses HTTP as the transport protocol. [en.wikipedia](https://en.wikipedia.org/wiki/SOAP)

### WSDL

**WSDL** stands for **Web Services Description Language**. It is an XML format that describes a web service’s endpoints, operations, messages, bindings, and protocols. [w3](https://www.w3.org/TR/wsdl.html)

**Airport analogy:** WSDL is like the airport’s official airline-operation manual: it tells partner systems exactly which operations exist—such as `CheckFlightStatus`, `BookFlight`, and `CancelBooking`—and what request/response format each operation accepts.

### SOAP example

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header>
    <Authentication>
      <ApiKey>airline-partner-key</ApiKey>
    </Authentication>
  </soap:Header>
  <soap:Body>
    <CheckFlightStatus>
      <flightNumber>AI-202</flightNumber>
      <date>2026-11-15</date>
    </CheckFlightStatus>
  </soap:Body>
</soap:Envelope>
```

### SOAP vs REST

| Feature | SOAP | REST |
|---|---|---|
| Type | Protocol | Architectural style |
| Message format | XML only | JSON, XML, plain text, etc. |
| Transport | HTTP, SMTP, TCP, etc. | Usually HTTP/HTTPS |
| Contract | WSDL | OpenAPI/Swagger commonly used |
| Security | WS-Security built in | HTTPS, OAuth, JWT, API keys |
| Complexity | Higher | Lower |
| Performance | Heavier XML messages | Lighter JSON messages |
| Best for | Enterprise, banking, legacy, strict contracts | Web/mobile/cloud APIs |
| State | Supports stateful operations | Typically stateless |

**Likely one-mark facts:**
- SOAP uses **XML**.
- SOAP messages are enclosed in a **SOAP envelope**.
- **WSDL** describes SOAP services.
- **WS-Security** is SOAP’s security standard.
- SOAP can use protocols other than HTTP, such as SMTP.
- REST is generally preferred for modern mobile/web APIs.

## 4. GraphQL APIs

**GraphQL** is a query language for APIs and a runtime for executing those queries. It allows clients to specify exactly which fields they need, reducing over-fetching and under-fetching. [spec.graphql](https://spec.graphql.org/October2021/)

### GraphQL operations

| Operation | Purpose | Airline example |
|---|---|---|
| Query | Read-only data fetch | Get flight, fare, and airport details |
| Mutation | Write operation followed by fetch | Create, update, or cancel booking |
| Subscription | Long-lived request receiving events over time | Live gate-change or flight-delay updates |

The GraphQL specification defines query as a read-only fetch, mutation as a write followed by a fetch, and subscription as a long-lived event-driven request. [spec.graphql](https://spec.graphql.org/September2025/)

### GraphQL schema example

```graphql
type Airport {
  iataCode: String!
  name: String!
  city: String!
  country: String!
}

type Flight {
  flightId: ID!
  flightNumber: String!
  airline: String!
  departureAirport: Airport!
  arrivalAirport: Airport!
  fare: Float!
  seatsAvailable: Int!
}

type Query {
  flight(flightId: ID!): Flight
  searchFlights(from: String!, to: String!, date: String!): [Flight!]!
}

type Mutation {
  createBooking(
    flightId: ID!
    passengerName: String!
    seatClass: String!
  ): Booking!
}

type Subscription {
  flightStatusChanged(flightId: ID!): FlightStatusUpdate!
}
```

### GraphQL query example

```graphql
query {
  flight(flightId: "AI-202") {
    flightNumber
    airline
    fare
    departureAirport {
      iataCode
      city
    }
    arrivalAirport {
      iataCode
      city
    }
  }
}
```

The client receives only the requested fields—useful for a mobile app that needs flight number, fare, and airport city but not the full internal flight record.

### GraphQL mutation example

```graphql
mutation {
  createBooking(
    flightId: "AI-202"
    passengerName: "Ravi Kumar"
    seatClass: "ECONOMY"
  ) {
    bookingId
    status
    seatNumber
  }
}
```

### GraphQL subscription example

```graphql
subscription {
  flightStatusChanged(flightId: "AI-202") {
    flightNumber
    status
    gate
    delayMinutes
  }
}
```

This can update an airport display board or passenger app when a flight is delayed or its gate changes.

### GraphQL terminology

| Term | Meaning |
|---|---|
| Schema | Defines available types, queries, mutations, subscriptions |
| Resolver | Function that fetches data for a field |
| Query | Read operation |
| Mutation | Write operation |
| Subscription | Real-time event stream |
| Type | Object definition, e.g. `Flight`, `Airport` |
| Field | Data attribute, e.g. `flightNumber` |
| Argument | Input to a field, e.g. `flightId` |
| Variable | External value passed into an operation |
| Fragment | Reusable selection set |
| Introspection | Ability to query the schema itself |
| Single endpoint | GraphQL commonly exposes one endpoint, e.g. `/graphql` |

### GraphQL vs REST

| Feature | REST | GraphQL |
|---|---|---|
| Data fetching | Server-defined response | Client-defined fields |
| Endpoints | Multiple resource endpoints | Usually one endpoint |
| Over-fetching | Possible | Avoided |
| Under-fetching | Possible; may need multiple calls | Avoided |
| Caching | HTTP caching is straightforward | More complex |
| Real-time support | Requires WebSocket/SSE separately | Built-in subscriptions |
| File upload | Standard HTTP multipart | Requires additional setup |
| Error handling | HTTP status codes | Usually HTTP 200 with `errors` field |
| Best for | Simple resource APIs, public APIs | Complex client-specific data needs |

## 5. API Framework Development

API framework development means building server-side APIs that expose business functionality to clients and other services.

### Popular frameworks

| Language | Framework | Use |
|---|---|---|
| Java | Spring Boot, Spring WebFlux | Airline booking/check-in microservices |
| Python | FastAPI, Flask, Django REST Framework | Flight-search and fare services |
| JavaScript | Express.js, NestJS | Airport dashboard and web APIs |
| Go | Gin, Echo | High-throughput gateway services |

### Spring Boot REST example

```java
@RestController
@RequestMapping("/api/v1/flights")
public class FlightController {

    private final FlightService flightService;

    public FlightController(FlightService flightService) {
        this.flightService = flightService;
    }

    @GetMapping
    public List<FlightResponse> searchFlights(
            @RequestParam String from,
            @RequestParam String to,
            @RequestParam String date) {
        return flightService.searchFlights(from, to, date);
    }

    @GetMapping("/{flightId}/status")
    public FlightStatusResponse getFlightStatus(@PathVariable String flightId) {
        return flightService.getStatus(flightId);
    }
}
```

### FastAPI example

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class BookingRequest(BaseModel):
    flight_id: str
    passenger_name: str
    seat_class: str

class BookingResponse(BaseModel):
    booking_id: str
    status: str

@app.post("/api/v1/bookings", response_model=BookingResponse, status_code=201)
def create_booking(request: BookingRequest):
    if request.seat_class not in {"ECONOMY", "BUSINESS", "FIRST"}:
        raise HTTPException(status_code=400, detail="Invalid seat class")
    return BookingResponse(
        booking_id="BK-90812",
        status="CONFIRMED"
    )
```

### API layers

| Layer | Responsibility | Airline example |
|---|---|---|
| Controller / Resource | Handles HTTP request/response | `FlightController` |
| Service | Business logic | Fare calculation, seat allocation |
| Repository / DAO | Database access | Fetch flights from PostgreSQL |
| DTO | Data transfer object | `FlightResponse`, `BookingRequest` |
| Entity | Database model | `Flight`, `Booking`, `Passenger` |
| Client | Calls external API | Payment gateway client |

### API documentation

| Tool | Used for |
|---|---|
| OpenAPI / Swagger | REST API documentation |
| Postman | API testing and collections |
| GraphQL Playground / GraphiQL | GraphQL exploration |
| AsyncAPI | Event-driven/API documentation |
| RAML | REST API modeling |

## 6. API Security

| Security control | Purpose | Airport/airline example |
|---|---|---|
| HTTPS/TLS | Encrypt data in transit | Protect passenger and payment details |
| API key | Identify an application | Airline partner integration |
| OAuth 2.0 | Delegated authorization | Passenger authorizes travel app |
| JWT | Stateless signed token | Contains passenger/agent claims |
| Basic authentication | Username/password; less preferred | Legacy internal service |
| mTLS | Mutual certificate-based authentication | Airline-to-airport secure integration |
| Rate limiting | Prevent abuse | Limit flight-search requests |
| Input validation | Prevent malformed/malicious input | Validate IATA airport code |
| CORS | Browser cross-origin access control | Airline website calling airline API |
| IP allowlisting | Permit known systems only | Airport operations system |
| Audit logging | Record API activity | Booking and cancellation audit trail |

### OAuth 2.0 flow

1. Passenger opens a travel app.
2. App redirects passenger to airline authorization server.
3. Passenger logs in and approves access.
4. Authorization server returns an authorization code.
5. App exchanges the code for an access token.
6. App calls the airline API with the access token.
7. API validates the token and returns booking data.

### JWT structure

A JWT has three Base64URL-encoded parts:

```text
header.payload.signature
```

- **Header:** Algorithm and token type.
- **Payload:** Claims such as passenger ID, role, expiry.
- **Signature:** Verifies token integrity.

## 7. Microservices and API Design

**Microservices architecture** divides an application into small, independently deployable services organized around business capabilities.

### Airline microservices

| Microservice | Responsibility | Example API |
|---|---|---|
| Flight Search Service | Finds available flights | `GET /flights/search` |
| Booking Service | Creates and manages bookings | `POST /bookings` |
| Payment Service | Processes payments/refunds | `POST /payments` |
| Check-in Service | Passenger check-in and boarding pass | `POST /check-ins` |
| Baggage Service | Tracks checked baggage | `GET /bags/{tagId}` |
| Loyalty Service | Frequent-flyer points | `GET /loyalty/{passengerId}` |
| Notification Service | Sends SMS/email/push | `POST /notifications` |
| Airport Operations Service | Gate, runway, delay information | `GET /airports/{code}/operations` |

### Communication styles

| Style | Description | Example |
|---|---|---|
| Synchronous | Caller waits for response | Booking service calls payment service |
| Asynchronous | Producer sends event; consumer processes later | Booking created event triggers notification |
| REST | HTTP request/response | `GET /flights/{id}` |
| gRPC | High-performance RPC using Protocol Buffers | Internal baggage-tracking calls |
| Message queue | Decoupled point-to-point messaging | RabbitMQ |
| Event streaming | Publish-subscribe event logs | Kafka |
| WebSocket | Full-duplex persistent connection | Live flight-status updates |

### API gateway responsibilities

- Request routing
- Authentication and authorization
- Rate limiting and throttling
- Load balancing
- Response caching
- Logging and monitoring
- SSL termination
- API version routing
- Request/response transformation

**Example:** All mobile-app requests go to `api.airline.com`. The gateway routes `/flights/**` to the Flight Search Service and `/bookings/**` to the Booking Service.

## 8. API Integration Across Platforms

APIs allow the same backend capabilities to serve many platforms.

| Platform | Consumer | API usage |
|---|---|---|
| Web | Airline website | Search flights, book tickets, manage booking |
| Mobile | Android/iOS app | Mobile check-in, boarding pass, notifications |
| Desktop | Airline agent portal | Manage bookings, refunds, group reservations |
| Airport kiosk | Self-check-in kiosk | Print boarding pass, select seat |
| Airport display board | Flight information system | Fetch departure/arrival status |
| Travel agency | Third-party booking platform | Search and book flights |
| Baggage system | Airport baggage-handling system | Update baggage status |
| Payment provider | Payment gateway | Authorize/capture/refund payment |

### Integration example

```text
Passenger mobile app
        |
        | HTTPS + JWT
        v
API Gateway
        |
        +--> Flight Search Service
        |
        +--> Booking Service
        |           |
        |           +--> Payment Service
        |           |
        |           +--> Notification Service
        |
        +--> Check-in Service
        |
        +--> Airport Operations Service
```

## 9. API Testing

| Test type | Purpose | Example |
|---|---|---|
| Unit testing | Test individual functions | Fare-calculation logic |
| Integration testing | Test service interactions | Booking + payment services |
| Contract testing | Ensure provider/consumer agreement | Mobile app and booking API |
| Functional testing | Validate business behavior | Correct fare for business class |
| Performance testing | Measure speed and load capacity | 10,000 concurrent flight searches |
| Security testing | Find vulnerabilities | Broken authentication, injection |
| Regression testing | Ensure old features still work | Existing booking flow |
| End-to-end testing | Test complete user journey | Search -> book -> pay -> check in |

### Tools

- **Postman:** REST API testing.
- **JUnit + Mockito:** Java unit testing.
- **REST Assured:** Java API testing.
- **pytest + httpx:** Python API testing.
- **Karate:** API test automation.
- **JMeter/k6:** Load and performance testing.
- **WireMock:** Mock external dependencies.

## 10. API Design Best Practices

- Use meaningful resource nouns: `/flights`, `/bookings`, `/airports`.
- Use plural nouns for collections: `/flights`, not `/flight`.
- Use HTTP methods correctly; do not put verbs in paths.
- Version APIs: `/api/v1/flights`.
- Use pagination for large lists: `?page=1&size=20`.
- Use filtering, sorting, and searching: `?from=MAA&to=DEL&sort=fare`.
- Return appropriate HTTP status codes.
- Validate all inputs.
- Use HTTPS everywhere.
- Do not expose internal IDs or database errors.
- Use consistent error-response format.
- Use idempotency keys for payment and booking operations.
- Document APIs with OpenAPI/Swagger.
- Monitor latency, error rate, throughput, and availability.

### Example error response

```json
{
  "timestamp": "2026-10-20T00:15:30Z",
  "status": 409,
  "error": "Conflict",
  "message": "Selected seat 12A is no longer available",
  "path": "/api/v1/bookings"
}
```

## 11. Rapid Revision: One-Liners

- **API** = Application Programming Interface.
- **REST** = Representational State Transfer.
- **SOAP** = Simple Object Access Protocol.
- **GraphQL** = Query language for APIs.
- **WSDL** = Web Services Description Language.
- **WSDL** describes SOAP service operations and messages. [w3](https://www.w3.org/TR/wsdl.html)
- SOAP uses **XML** messages and a **SOAP envelope**. [en.wikipedia](https://en.wikipedia.org/wiki/SOAP)
- GraphQL operations are **query**, **mutation**, and **subscription**. [spec.graphql](https://spec.graphql.org/October2021/)
- GraphQL **query** reads data.
- GraphQL **mutation** writes data.
- GraphQL **subscription** receives real-time events.
- REST commonly uses **HTTP + JSON**.
- REST is **stateless**.
- `GET` = read; `POST` = create; `PUT` = replace; `PATCH` = partial update; `DELETE` = remove.
- `201 Created` is returned after successful resource creation.
- `401 Unauthorized` means authentication failed.
- `403 Forbidden` means authenticated caller lacks permission.
- `429 Too Many Requests` means rate limit exceeded.
- **OpenAPI/Swagger** documents REST APIs.
- **OAuth 2.0** is used for delegated authorization.
- **JWT** = JSON Web Token.
- **API gateway** routes, secures, and manages API traffic.
- **Microservices** are independently deployable services.
- **Kafka** is commonly used for event streaming.
- **gRPC** uses Protocol Buffers and is suited for internal high-performance communication.
- APIs enable web, mobile, desktop, kiosk, and partner-system integration.

## 12. Probable One-Mark Questions

1. **What does API stand for?**  
   Application Programming Interface.

2. **What does REST stand for?**  
   Representational State Transfer.

3. **What does SOAP stand for?**  
   Simple Object Access Protocol.

4. **Which protocol uses XML messages and a SOAP envelope?**  
   SOAP.

5. **Which document describes a SOAP web service?**  
   WSDL.

6. **What are the three GraphQL operation types?**  
   Query, mutation, subscription.

7. **Which GraphQL operation is used to create a flight booking?**  
   Mutation.

8. **Which GraphQL operation is used for live gate-change updates?**  
   Subscription.

9. **Which HTTP method is used to fetch flight status?**  
   GET.

10. **Which HTTP method is used to create a booking?**  
   POST.

11. **Which HTTP method is used for a partial booking update?**  
   PATCH.

12. **Which HTTP status code indicates successful resource creation?**  
   201.

13. **Which HTTP status code indicates a rate-limit violation?**  
   429.

14. **Which HTTP status code indicates the requested booking was not found?**  
   404.

15. **Is REST a protocol or architectural style?**  
   Architectural style.

16. **Which specification is commonly used to document REST APIs?**  
   OpenAPI/Swagger.

17. **Which security standard is associated with SOAP?**  
   WS-Security.

18. **Which token format commonly contains claims such as passenger ID and expiry?**  
   JWT.

19. **Which component provides a single entry point for microservices APIs?**  
   API gateway.

20. **Which architecture style divides an airline system into booking, payment, check-in, and baggage services?**  
   Microservices.

21. **Which GraphQL problem does GraphQL reduce compared with REST?**  
   Over-fetching and under-fetching.

22. **Which API style is generally best for legacy enterprise integrations requiring strict contracts?**  
   SOAP.

23. **Which API style allows the client to request only selected fields?**  
   GraphQL.

24. **Which HTTP header is commonly used to send a JWT?**  
   `Authorization: Bearer <token>`.

25. **Which API design practice prevents clients from retrieving millions of flights in one response?**  
   Pagination.
