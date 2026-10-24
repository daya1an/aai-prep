# API Programming — Advanced Architecture & Engineering Notes

At a 3+ year level, API expertise means designing **stable contracts, scalable service boundaries, resilient integrations, observable platforms, and governed API lifecycles**—not merely exposing REST endpoints. This note emphasizes advanced REST/SOAP/GraphQL design, contract-first development, event-driven APIs, microservice integration, performance, versioning, and production-grade Spring Boot patterns. OpenAPI is a language-agnostic specification for HTTP APIs that lets consumers understand and use a service without reading source code, while AsyncAPI describes message-driven APIs across protocols such as Kafka, AMQP, MQTT, and WebSockets. [swagger](https://swagger.io/specification/v3.2/)

## 1. API as a Product and Contract

An API is a **product boundary** and a **contract**, not simply a controller layer. It must remain useful when consumers, teams, platforms, and deployment models change.

### API product dimensions

| Dimension | Design question | Airline example |
|---|---|---|
| Consumer | Who consumes it? | Mobile app, web portal, agent, partner, internal service |
| Capability | What business capability does it expose? | Search flights, book ticket, check in |
| Contract | What is the stable interface? | OpenAPI/GraphQL schema/AsyncAPI document |
| Data ownership | Who owns each entity? | Booking service owns bookings |
| Consistency | What consistency is promised? | Booking creation is strongly consistent; loyalty points eventually consistent |
| Availability | What availability is required? | Check-in API: 99.95% |
| Latency | What response-time target applies? | Flight search p95 < 300 ms |
| Change policy | How are breaking changes handled? | URI major version + deprecation window |
| Supportability | Who troubleshoots failures? | API owner, on-call rotation, runbook |

### Contract-first workflow

```text
Identify capability
   -> Define consumer journeys
   -> Design resource/event model
   -> Write OpenAPI / GraphQL / AsyncAPI contract
   -> Review with consumers
   -> Publish mock server
   -> Implement provider
   -> Contract-test provider and consumers
   -> Release and monitor usage
```

**Benefit:** The mobile team can build against a mock flight-search API while the backend team implements it independently.

## 2. Advanced REST API Design

### Resource-oriented thinking

Model **business nouns and state transitions**, not database tables or UI screens.

| Poor design | Better design | Reason |
|---|---|---|
| `POST /createBooking` | `POST /bookings` | Resource creation |
| `GET /getFlight?id=AI202` | `GET /flights/AI-202` | Resource retrieval |
| `POST /cancelBooking?id=BK1` | `POST /bookings/BK-1/cancellations` | State transition |
| `POST /updatePassenger` | `PATCH /passengers/PAX-1` | Partial update |
| `GET /flightSearchResults` | `GET /flights?from=MAA&to=DEL` | Query resources |

### Resource relationships

```text
/api/v1/airports/{airportCode}
/api/v1/flights/{flightId}
/api/v1/flights/{flightId}/seats
/api/v1/bookings/{bookingReference}
/api/v1/bookings/{bookingReference}/passengers
/api/v1/bookings/{bookingReference}/payments
/api/v1/bookings/{bookingReference}/baggage
/api/v1/passengers/{passengerId}/loyalty-account
```

**Rules:**
- Use stable business identifiers where possible: booking reference, IATA airport code.
- Avoid exposing internal sequential database IDs.
- Limit nesting to two levels; deeper paths become brittle.
- Use query parameters for filtering, sorting, pagination, and projections.
- Use a controller resource for non-CRUD transitions: `/bookings/{id}/cancellations`.

### HTTP semantics in depth

| Method | Semantics | Idempotent | Safe | Airline example |
|---|---|---:|---:|---|
| GET | Retrieve representation | Yes | Yes | Get flight status |
| HEAD | Retrieve headers only | Yes | Yes | Check resource existence |
| POST | Create/process non-idempotent operation | No | No | Create booking |
| PUT | Full replacement | Yes | No | Replace passenger profile |
| PATCH | Partial update | Not guaranteed | No | Update seat preference |
| DELETE | Remove resource | Yes | No | Cancel booking |
| OPTIONS | Discover communication options | Yes | Yes | CORS preflight |

### Conditional requests

Use `ETag`, `If-Match`, `If-None-Match`, `If-Modified-Since`, and `If-Unmodified-Since` to prevent lost updates and improve caching.

```http
GET /api/v1/bookings/BK-90812

HTTP/1.1 200 OK
ETag: "v17"
Cache-Control: private, max-age=30
```

```http
PATCH /api/v1/bookings/BK-90812
If-Match: "v17"
Content-Type: application/json

{ "seatPreference": "WINDOW" }
```

If another user modified the booking, return:

```http
HTTP/1.1 412 Precondition Failed
```

### Advanced status-code usage

| Status | Use | Airline example |
|---:|---|---|
| 200 | Successful request with body | Flight status returned |
| 201 | Resource created | Booking created |
| 202 | Async processing accepted | Refund request queued |
| 204 | Success without body | Booking deleted |
| 304 | Not modified | Cached flight data still valid |
| 400 | Malformed request | Invalid date format |
| 401 | Authentication required/failed | Missing/expired token |
| 403 | Authenticated but forbidden | Agent cannot access another airline’s booking |
| 404 | Resource absent | Unknown booking reference |
| 405 | Method not allowed | DELETE on read-only flight endpoint |
| 406 | Not acceptable | Client requests unsupported media type |
| 409 | State conflict | Seat already taken |
| 410 | Gone | Retired API/resource |
| 412 | Precondition failed | ETag mismatch |
| 415 | Unsupported media type | XML sent to JSON-only API |
| 422 | Semantic validation failure | Departure date before today |
| 428 | Precondition required | Missing `If-Match` |
| 429 | Rate limited | Too many searches |
| 500 | Server error | Unexpected exception |
| 502 | Bad gateway | Upstream service invalid response |
| 503 | Service unavailable | Check-in service down |
| 504 | Gateway timeout | Fare service timed out |

### Error contract

Use one consistent, machine-readable error schema.

```json
{
  "type": "https://api.airline.com/errors/seat-unavailable",
  "title": "Seat unavailable",
  "status": 409,
  "detail": "Seat 12A on flight AI-202 is no longer available.",
  "instance": "/api/v1/bookings",
  "traceId": "req-77123",
  "errors": [
    {
      "field": "seatNumber",
      "code": "SEAT_ALREADY_RESERVED",
      "message": "Select another seat."
    }
  ]
}
```

This resembles RFC 9457/Problem Details style: stable `type`, human-readable `title/detail`, machine-readable `errors`, and a `traceId` for support.

## 3. API Modeling and Schema Design

### DTO, entity, and domain model separation

| Layer | Purpose | Airline example |
|---|---|---|
| API DTO | External contract | `CreateBookingRequest`, `FlightResponse` |
| Domain model | Business rules | `Booking`, `Flight`, `SeatInventory` |
| Persistence entity | Database mapping | `BookingEntity`, `FlightEntity` |
| Integration model | External system contract | Payment-gateway request/response |

Never expose JPA entities directly; they leak internal fields, create lazy-loading issues, and make versioning difficult.

### Schema evolution rules

| Change | Compatibility |
|---|---|
| Add optional response field | Backward compatible |
| Add optional request field with default | Usually compatible |
| Remove/rename response field | Breaking |
| Change field type/format | Breaking |
| Make optional field required | Breaking |
| Add enum value | Potentially breaking |
| Narrow validation | Breaking |
| Add new endpoint | Usually compatible |
| Add new error code | Usually compatible if documented |

### OpenAPI example

OpenAPI describes endpoints, operations, parameters, request/response schemas, authentication, and other API details in YAML or JSON. [swagger](https://swagger.io/docs/specification/v3_0/about/)

```yaml
openapi: 3.0.3
info:
  title: Airline Booking API
  version: 1.2.0
paths:
  /api/v1/flights:
    get:
      summary: Search flights
      parameters:
        - name: from
          in: query
          required: true
          schema:
            type: string
            minLength: 3
            maxLength: 3
        - name: to
          in: query
          required: true
          schema:
            type: string
        - name: date
          in: query
          required: true
          schema:
            type: string
            format: date
      responses:
        "200":
          description: Flight search results
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/FlightSearchResponse"
components:
  schemas:
    Flight:
      type: object
      required: [flightId, flightNumber, fare]
      properties:
        flightId:
          type: string
        flightNumber:
          type: string
        fare:
          type: number
          format: double
        seatsAvailable:
          type: integer
          minimum: 0
```

### Spring Boot implementation

Spring Boot helps create stand-alone, production-grade Spring-based applications with an opinionated approach and minimal configuration. [docs.spring](https://docs.spring.io/spring-boot/index.html)

```java
@RestController
@RequestMapping("/api/v1/flights")
public class FlightController {

    private final FlightSearchService searchService;

    public FlightController(FlightSearchService searchService) {
        this.searchService = searchService;
    }

    @GetMapping
    public ResponseEntity<FlightSearchResponse> search(
            @RequestParam @Pattern(regexp = "[A-Z]{3}") String from,
            @RequestParam @Pattern(regexp = "[A-Z]{3}") String to,
            @RequestParam @DateTimeFormat(iso = ISO.DATE) LocalDate date,
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size) {

        FlightSearchResponse response =
                searchService.search(from, to, date, PageRequest.of(page, size));

        return ResponseEntity.ok()
                .cacheControl(CacheControl.maxAge(Duration.ofSeconds(30)))
                .body(response);
    }
}
```

### springdoc-openapi

`springdoc-openapi` generates OpenAPI 3 documentation and Swagger UI for Spring Boot from controllers, annotations, and constraints. [springdoc](https://springdoc.org/)

```java
@Operation(
    summary = "Search available flights",
    description = "Returns flights matching route and date",
    responses = {
        @ApiResponse(responseCode = "200", description = "Search successful"),
        @ApiResponse(responseCode = "400", description = "Invalid search criteria")
    }
)
@GetMapping
public FlightSearchResponse searchFlights(...) { ... }
```

## 4. API Versioning and Lifecycle

### Versioning strategies

| Strategy | Example | When to use |
|---|---|---|
| URI major version | `/v1/flights` | Public APIs, simple consumer migration |
| Header version | `API-Version: 2026-11-01` | Frequent minor evolution |
| Media-type version | `Accept: application/vnd.airline.v2+json` | Content negotiation-heavy APIs |
| Query version | `?version=2` | Testing/debugging only |
| Date-based version | `API-Version: 2026-11-01` | Calendar-driven enterprise APIs |

**Recommended:** URI version for breaking changes; header/date version for additive evolution.

### API lifecycle

```text
Design -> Review -> Mock -> Implement -> Test
      -> Secure -> Publish -> Monitor
      -> Deprecate -> Migrate -> Retire
```

### Deprecation response

```http
HTTP/1.1 200 OK
Deprecation: true
Sunset: Wed, 31 Dec 2026 23:59:59 GMT
Link: <https://developer.airline.com/migrate-v1-to-v2>; rel="deprecation"
```

### API compatibility gates

Before release, validate:

- OpenAPI/GraphQL schema diff
- Backward compatibility
- Contract tests
- Consumer impact analysis
- Breaking-change approval
- Documentation update
- Migration guide
- Version-usage telemetry

## 5. Advanced Integration Patterns

### Synchronous integration

| Pattern | Use when | Airline example |
|---|---|---|
| Request-response | Caller needs immediate result | Check seat availability |
| API composition | Aggregate multiple services | Flight + fare + seat availability |
| Backend for Frontend | Tailor API per client | Mobile BFF, web BFF |
| API gateway | Central external entry point | `api.airline.com` |
| Sidecar | Platform capabilities beside service | Service mesh proxy |

### Asynchronous integration

| Pattern | Use when | Airline example |
|---|---|---|
| Fire-and-forget | Caller does not need result | Send notification |
| Request/reply via messaging | Async work with correlated response | Refund processing |
| Publish-subscribe | Multiple consumers need event | `BookingCreated` |
| Event-carried state transfer | Consumer needs data without callback | Flight-status update |
| Claim check | Store large payload externally, send reference | Large itinerary document |
| Outbox | Reliable event publication after DB commit | Booking-created event |
| Saga | Multi-service business transaction | Book, reserve seat, pay, confirm |
| Process manager | Long-running workflow | Rebooking after cancellation |

### API composition example

```text
Mobile BFF
   |
   +--> Flight Search Service
   |
   +--> Pricing Service
   |
   +--> Seat Inventory Service
   |
   v
Combined response
```

Use parallel calls, timeouts, partial-failure policies, and caching. Do not create a synchronous chain of 6+ services for a user-facing request.

### Backend for Frontend

| BFF | Consumers | Response shape |
|---|---|---|
| Mobile BFF | Android/iOS | Compact fields, image URLs, push-friendly payload |
| Web BFF | Browser | Larger payload, SEO/analytics fields |
| Agent BFF | Desktop portal | Booking-management and refund operations |
| Partner BFF | Travel agencies | Standardized partner schema |

## 6. Microservices API Boundaries

### Service decomposition

| Service | Owns | API examples |
|---|---|---|
| Flight Catalog | Flights, routes, schedules | `GET /flights`, `GET /airports` |
| Inventory | Seats/availability | `GET /flights/{id}/seats` |
| Pricing | Fare rules/pricing | `POST /quotes` |
| Booking | Bookings/passengers | `POST /bookings` |
| Payment | Payments/refunds | `POST /payments` |
| Check-in | Boarding passes | `POST /check-ins` |
| Baggage | Bag tracking | `GET /bags/{tagId}` |
| Loyalty | Miles/tiers | `GET /loyalty/{passengerId}` |
| Notification | Messages | `POST /notifications` |

### Boundary rules

- One team owns one service and its API contract.
- A service owns its database; no shared database integration.
- Communicate through contracts, not internal models.
- Avoid distributed monoliths: do not require synchronous calls for every operation.
- Use domain events for cross-service workflows.
- Keep APIs aligned with business capabilities, not technical layers.

### Anti-corruption layer

When integrating a legacy SOAP system, translate its model into your domain model.

```text
Modern Booking API
        |
        v
Anti-Corruption Layer
        |
        v
Legacy Reservation SOAP Service
```

This prevents legacy field names, XML structures, and status codes from leaking into modern APIs.

## 7. Event-Driven API Design

### REST vs events

| Concern | REST/gRPC | Event-driven API |
|---|---|---|
| Interaction | Request-response | Publish-subscribe / event stream |
| Coupling | Consumer knows provider endpoint | Producer does not know consumers |
| Availability dependency | Consumer needs provider online | Consumer processes when available |
| Data consistency | Immediate response | Eventual consistency |
| Best for | Queries, commands needing result | State changes, workflows, integrations |

### Airline event catalog

| Event | Producer | Consumers |
|---|---|---|
| `FlightScheduled` | Flight catalog | Search, pricing, airport ops |
| `FlightDelayed` | Operations | Passenger app, notification, rebooking |
| `SeatReserved` | Inventory | Booking, seat map |
| `BookingCreated` | Booking | Payment, loyalty, notification |
| `PaymentCompleted` | Payment | Booking, ticketing, loyalty |
| `PaymentFailed` | Payment | Booking, notification |
| `CheckInCompleted` | Check-in | Baggage, boarding, notification |
| `BaggageScanned` | Airport baggage system | Passenger app, baggage service |
| `GateChanged` | Airport operations | Display boards, mobile apps |

### Event envelope

```json
{
  "eventId": "evt-01HZY8",
  "eventType": "FLIGHT_DELAYED",
  "eventVersion": "2.0",
  "occurredAt": "2026-11-15T07:45:00Z",
  "producer": "flight-operations-service",
  "correlationId": "req-77123",
  "causationId": "evt-01HZY6",
  "data": {
    "flightId": "AI-202",
    "delayMinutes": 35,
    "newDepartureTime": "2026-11-15T09:05:00+05:30",
    "gate": "A4"
  }
}
```

### AsyncAPI example

AsyncAPI is protocol-agnostic and can describe APIs over Kafka, AMQP, MQTT, WebSockets, HTTP, and other transports. [asyncapi](https://www.asyncapi.com/docs/reference/specification/v3.0.0)

```yaml
asyncapi: 3.0.0
info:
  title: Airline Booking Events
  version: 1.0.0
channels:
  bookingEvents:
    address: booking-events
    messages:
      BookingCreated:
        payload:
          type: object
          properties:
            bookingReference:
              type: string
            flightId:
              type: string
            passengerId:
              type: string
operations:
  publishBookingCreated:
    action: send
    channel:
      $ref: "#/channels/bookingEvents"
    messages:
      - $ref: "#/channels/bookingEvents/messages/BookingCreated"
```

## 8. GraphQL at Production Scale

### When GraphQL fits

GraphQL is strong when multiple clients need different projections of the same domain data.

**Example:** Mobile app needs flight number, fare, and departure airport; agent portal needs full booking, passenger, seat map, and payment details.

```graphql
query MobileFlightView($flightId: ID!) {
  flight(flightId: $flightId) {
    flightNumber
    fare
    departureAirport {
      iataCode
      city
    }
  }
}
```

### GraphQL schema design

```graphql
type Flight @key(fields: "flightId") {
  flightId: ID!
  flightNumber: String!
  airline: Airline!
  departureAirport: Airport!
  arrivalAirport: Airport!
  fare: Money!
  seatsAvailable: Int!
}

type Booking @key(fields: "bookingReference") {
  bookingReference: ID!
  status: BookingStatus!
  flight: Flight!
  passenger: Passenger!
}

type Query {
  flight(flightId: ID!): Flight
  searchFlights(input: FlightSearchInput!): FlightConnection!
}

type Mutation {
  createBooking(input: CreateBookingInput!): CreateBookingPayload!
}

type Subscription {
  flightStatusChanged(flightId: ID!): FlightStatusUpdate!
}
```

### Advanced GraphQL concerns

| Concern | Problem | Solution |
|---|---|---|
| N+1 resolvers | Many database calls per nested field | DataLoader batching/caching |
| Query depth abuse | Deep nested query overwhelms server | Depth limiting |
| Query complexity | Expensive computed fields | Complexity scoring/limits |
| Arbitrary queries | Unpredictable backend load | Persisted queries |
| Schema sprawl | Overlapping ownership | Federation/domain ownership |
| Caching | POST-based GraphQL complicates HTTP caching | APQ, response cache, CDN edge cache |
| Authorization | Field-level data leakage | Field-level authz and schema design |
| Error leakage | Internal errors exposed | Masked errors + structured error extensions |

### Federation

Each domain owns a subgraph:

| Subgraph | Owns |
|---|---|
| Flight | `Flight`, `Airline` |
| Airport | `Airport`, terminal/gate |
| Booking | `Booking`, `Passenger` |
| Loyalty | Loyalty account/miles |

The gateway composes subgraphs into one graph for clients, while teams retain independent ownership.

## 9. SOAP in Modern Integration

SOAP remains relevant for regulated, legacy, and enterprise integrations requiring strict contracts and mature WS-* standards.

### When to use SOAP

| Situation | Why SOAP fits |
|---|---|
| Legacy airline-reservation system | Existing WSDL-based integration |
| Banking/payment partner | Strict contract and WS-Security |
| Government/aviation authority | Formal XML contract |
| Enterprise B2B integration | Reliable messaging and standards |

### Modernization strategy

Do not expose SOAP directly to mobile/web clients. Place an **anti-corruption layer/API facade** between modern consumers and legacy SOAP.

```text
Mobile/Web -> REST/GraphQL API -> Anti-corruption layer -> Legacy SOAP
```

### SOAP integration checklist

- Validate WSDL and XSD compatibility.
- Use XML schema validation.
- Handle SOAP faults explicitly.
- Set timeouts and retry policies.
- Use WS-Security where required.
- Avoid XML External Entity injection.
- Monitor SOAP operation latency and fault rates.
- Cache static reference data where valid.

## 10. API Gateway and Edge Design

### Gateway responsibilities

| Responsibility | Airline example |
|---|---|
| Routing | `/flights/**` to flight service |
| Authentication | Validate JWT/API key |
| Authorization | Enforce scopes |
| Rate limiting | Protect expensive search API |
| Caching | Cache airport master data |
| Transformation | Convert legacy XML to JSON |
| Protocol translation | External REST to internal gRPC |
| Observability | Logs, metrics, traces |
| Canary release | Route 5% to v2 |
| Request validation | Reject malformed requests early |

### Gateway vs BFF vs microservice

| Component | Responsibility |
|---|---|
| API gateway | Cross-cutting edge concerns |
| BFF | Client-specific aggregation and shape |
| Microservice | Business capability and data ownership |
| Service mesh | Service-to-service traffic, mTLS, resilience |

## 11. Resilience and Reliability

### Failure modes

| Failure | Airline example | Mitigation |
|---|---|---|
| Timeout | Fare service slow | Deadline/timeout |
| Transient failure | Network blip | Retry with backoff |
| Dependency outage | Payment provider down | Circuit breaker/fallback |
| Traffic spike | Sale-day booking surge | Rate limiting/autoscaling |
| Cascading failure | Booking calls overwhelm inventory | Bulkhead/load shedding |
| Partial failure | One airport service down | Graceful degradation |
| Poison message | Malformed booking event | DLQ/poison-pill handling |
| Data inconsistency | Payment succeeded but booking pending | Saga/outbox/reconciliation |

### Resilience patterns

| Pattern | Purpose | Airline example |
|---|---|---|
| Timeout | Bound waiting time | Fare API: 800 ms |
| Retry | Handle transient errors | Retry `503` with backoff |
| Exponential backoff | Avoid retry storm | 100 ms, 200 ms, 400 ms |
| Jitter | Spread retry load | Add random delay |
| Circuit breaker | Stop calls to unhealthy dependency | Stop payment calls after failures |
| Bulkhead | Isolate resource pools | Separate pools for search and booking |
| Fallback | Provide degraded response | Last-known flight status |
| Cache-aside | Reduce dependency load | Airport details |
| Load shedding | Protect critical traffic | Prioritize payment over anonymous search |
| Hedging | Reduce tail latency | Duplicate slow request after threshold |
| Graceful degradation | Preserve core capability | Booking works if loyalty is down |

### Circuit-breaker states

```text
CLOSED -> failures exceed threshold -> OPEN
OPEN -> cooldown expires -> HALF-OPEN
HALF-OPEN -> probe succeeds -> CLOSED
HALF-OPEN -> probe fails -> OPEN
```

### Retry rules

Retry only when:

- Failure is transient.
- Operation is idempotent or has an idempotency key.
- Retry budget is configured.
- Backoff and jitter are used.
- The caller can tolerate delay.

Never blindly retry `POST /payments` without idempotency protection.

## 12. Consistency, Idempotency and Sagas

### Idempotency

An API operation is idempotent when repeating it produces the same final state.

| Operation | Idempotent? | Handling |
|---|---:|---|
| `GET /flights/AI-202` | Yes | Safe |
| `PUT /passengers/PAX-1` | Yes | Full replacement |
| `DELETE /bookings/BK-1` | Yes | Same final state |
| `POST /bookings` | No by default | Idempotency key required |
| `POST /payments` | No by default | Idempotency key required |

### Idempotency-key implementation

```http
POST /api/v1/bookings
Idempotency-Key: 9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d
```

Server-side flow:

```text
Receive request + idempotency key
   -> Check key store
   -> If found, return stored response
   -> If new, execute business operation
   -> Store response against key
   -> Return response
```

Store:

- Idempotency key
- Request fingerprint/hash
- Response/status
- Expiry time
- Lock/processing state

Return `409 Conflict` if the same key is reused with a different payload.

### Saga pattern

```text
1. Booking service creates PENDING booking
2. Inventory service reserves seat
3. Payment service authorizes payment
4. Booking service confirms booking

Failure after payment:
1. Payment service refunds
2. Inventory service releases seat
3. Booking service marks booking cancelled
```

| Saga style | Description | Trade-off |
|---|---|---|
| Orchestration | Central coordinator commands steps | Easier to reason; coordinator can become bottleneck |
| Choreography | Services react to events | Loose coupling; harder end-to-end visibility |

### Outbox pattern

```java
@Transactional
public Booking createBooking(CreateBookingCommand command) {
    Booking booking = bookingRepository.save(toBooking(command));
    outboxRepository.save(OutboxEvent.of("BOOKING_CREATED", booking));
    return booking;
}
```

A separate relay publishes outbox events to Kafka. This avoids inconsistency when the database transaction succeeds but the message broker publish fails.

## 13. Performance Engineering

### Latency budget

```text
Flight-search SLO: p95 < 300 ms

Gateway                 10 ms
Authentication           5 ms
Flight Search Service   80 ms
Pricing Service         70 ms
Inventory Service       60 ms
Cache/DB access         40 ms
Serialization           15 ms
Network overhead        20 ms
```

Set downstream timeouts below the remaining end-to-end budget.

### Optimization techniques

| Technique | Description | Airline example |
|---|---|---|
| Pagination | Limit result size | 20 flights/page |
| Cursor pagination | Stable paging for changing data | Flight-search results |
| Field projection | Return required fields only | Mobile gets fare/time only |
| Compression | Gzip/Brotli | Compress large flight lists |
| HTTP/2 | Multiplex requests | Mobile parallel calls |
| Connection pooling | Reuse DB/HTTP connections | Booking DB pool |
| Async/non-blocking I/O | Free threads while waiting | WebFlux/Netty |
| Parallel fan-out | Call dependencies concurrently | Flight + fare + inventory |
| Caching | Avoid repeated computation | Airport data, fare rules |
| Read replicas | Scale read traffic | Flight-search DB |
| Materialized view | Precompute expensive query | Departure-board data |
| Database index | Speed up filtered query | Route/date index |
| Batch API | Reduce request count | Fetch 20 airports at once |
| Edge caching | Serve from nearest location | Airport reference data |

### Caching strategies

| Strategy | Description | Airline example |
|---|---|---|
| Client cache | Browser/app cache | Airport list |
| CDN cache | Edge cache | Static fare rules |
| Gateway cache | API response cache | Popular search route |
| Redis cache | Distributed cache | Seat availability |
| Cache-aside | Application manages cache | Airport details |
| Read-through | Cache loads DB data | Reference data |
| Write-through | Write cache and DB together | Critical config |
| TTL | Time-based expiry | Flight status: 15 seconds |
| Stale-while-revalidate | Serve stale while refreshing | Airport master data |

**Never cache personalized booking/payment data in a shared cache without strict key isolation.**

## 14. Observability

### Three pillars

| Pillar | Purpose | Airline example |
|---|---|---|
| Logs | Discrete events | Booking failed due to seat conflict |
| Metrics | Numeric time series | Search p95 latency |
| Traces | Cross-service request path | Mobile -> gateway -> booking -> payment |

### Golden signals

| Signal | Meaning | Airline example |
|---|---|---|
| Latency | Response duration | Search p95 < 300 ms |
| Traffic | Request rate | 5,000 searches/sec |
| Errors | Failed requests | 0.1% booking error rate |
| Saturation | Resource pressure | DB pool 80% utilized |

### API metrics to track

- Request rate by route/version/status
- Error rate by route/client
- p50/p95/p99 latency
- Upstream dependency latency
- Cache hit ratio
- Rate-limit rejections
- Idempotency replay count
- Saga compensation rate
- Kafka consumer lag
- DLQ depth
- API version traffic
- Certificate expiry
- Contract-test failures

### Trace propagation

```http
X-Request-Id: req-77123
X-Correlation-Id: booking-flow-8891
traceparent: 00-4bf92f...-00f067...-01
```

Propagate context through gateway, BFF, services, message headers, and async consumers.

## 15. API Security for Engineers

Even outside a security-focused unit, API engineers must apply baseline controls.

| Control | Purpose | Airline example |
|---|---|---|
| HTTPS/TLS | Protect data in transit | Mobile app to booking API |
| OAuth 2.0 | Delegated authorization | Passenger authorizes travel app |
| OIDC | Authentication/identity | Passenger SSO |
| JWT | Stateless signed token | Booking API access token |
| API key | Identify application/partner | Travel-agency integration |
| mTLS | Mutual service authentication | Airline-to-airport integration |
| Scope-based authorization | Limit token permissions | `bookings:read`, `bookings:write` |
| Object-level authorization | Prevent IDOR | Passenger accesses only own booking |
| Input validation | Reject malformed data | Validate IATA code/date |
| Rate limiting | Prevent abuse | Search quota |
| Idempotency key | Prevent duplicate booking/payment | Retry-safe booking |
| CORS | Browser origin control | Airline web app |
| Audit logging | Trace sensitive actions | Booking cancellation |

## 16. API Testing Strategy

| Test type | Purpose | Tools |
|---|---|---|
| Unit test | Business logic | JUnit, Mockito, pytest |
| Slice test | Controller/service layer | `@WebMvcTest`, FastAPI TestClient |
| Integration test | Real dependencies | Testcontainers |
| Contract test | Provider/consumer compatibility | Pact, Spring Cloud Contract |
| Schema validation | Contract correctness | OpenAPI validators, jsonschema |
| End-to-end test | Complete journey | Search -> book -> pay -> check-in |
| Performance test | Load/latency | k6, JMeter, Locust |
| Chaos test | Failure behavior | Chaos Mesh, Litmus |
| Fuzz test | Malformed inputs | Schemathesis, RESTler |
| Compatibility test | Version migration | Consumer-driven contracts |

### Spring Boot contract-test idea

Consumer expects:

```json
{
  "bookingReference": "BK-90812",
  "status": "CONFIRMED",
  "fare": 7500
}
```

Provider must satisfy this contract before deployment. Removing `fare` fails the consumer build.

### Spring REST Docs

Spring REST Docs combines hand-written documentation with auto-generated snippets from Spring MVC Test or WebTestClient, producing accurate REST documentation from tests. [docs.spring](https://docs.spring.io/spring-restdocs/docs/current/reference/htmlsingle/)

## 17. API Governance

| Area | Practice |
|---|---|
| API catalog | Register API, owner, version, environment |
| Design standards | Naming, errors, pagination, security |
| Design review | Review contract before implementation |
| Style guide | Consistent resource and field conventions |
| Version policy | Define breaking-change rules |
| Deprecation policy | Define notice and retirement period |
| Documentation | Always current and executable |
| SDK generation | Generate client SDKs from contract |
| Change management | Schema diff and consumer impact |
| Usage analytics | Track consumers and deprecated-version usage |
| Quality gates | Contract, performance, security checks |
| Runbooks | Operational troubleshooting guidance |

## 18. Rapid Revision: Advanced One-Liners

- **API contract** defines the stable interaction boundary between provider and consumer.
- **Contract-first** design defines OpenAPI/GraphQL/AsyncAPI before implementation.
- **OpenAPI** describes HTTP APIs; **AsyncAPI** describes message-driven APIs. [swagger](https://swagger.io/specification/v3.2/)
- **BFF** tailors an API for a specific frontend.
- **API gateway** handles cross-cutting edge concerns.
- **Service mesh** handles service-to-service traffic concerns.
- **Anti-corruption layer** isolates legacy-system models from modern domains.
- **API composition** aggregates multiple service responses.
- **Saga** coordinates multi-service workflows using compensations.
- **Outbox pattern** reliably publishes events after a database transaction.
- **Idempotency key** makes retries safe for non-idempotent POST operations.
- **Cursor pagination** is preferable for large changing datasets.
- **ETag + If-Match** provides optimistic concurrency control.
- **HATEOAS** lets responses guide available next actions.
- **GraphQL query** reads data; **mutation** writes data; **subscription** streams events.
- **DataLoader** mitigates GraphQL N+1 resolver calls.
- **Persisted queries** restrict clients to approved GraphQL operations.
- **Federation** composes domain-owned GraphQL subgraphs.
- **gRPC** uses Protobuf and HTTP/2 for efficient service-to-service communication.
- **Retry only transient failures**, preferably with backoff and jitter.
- **Circuit breaker** prevents repeated calls to an unhealthy dependency.
- **Bulkhead** isolates resource pools to prevent cascading failure.
- **Timeouts must be lower than the end-to-end latency budget.**
- **Cache-aside** is application-managed caching.
- **Stale-while-revalidate** balances freshness and latency.
- **Correlation ID** links a request across synchronous and asynchronous services.
- **Consumer-driven contract testing** prevents provider changes from breaking consumers.
- **API governance** ensures ownership, consistency, versioning, and lifecycle control.

## 19. Maximum-Level Probable Questions

1. **Why should JPA entities not be exposed directly through APIs?**  
   They leak internal structure, cause lazy-loading issues, and make contract evolution difficult.

2. **What is the difference between an API gateway and a BFF?**  
   Gateway handles cross-cutting edge concerns; BFF tailors API shape for a specific frontend.

3. **When should you use asynchronous messaging instead of REST?**  
   When decoupling, eventual consistency, fan-out, or resilience to consumer downtime is more important than an immediate response.

4. **What is an anti-corruption layer?**  
   A translation boundary that prevents legacy/external models from leaking into a modern domain model.

5. **What is the outbox pattern?**  
   Writing a domain event to an outbox table in the same DB transaction, then publishing it reliably.

6. **What is the difference between orchestration and choreography?**  
   Orchestration uses a central coordinator; choreography uses services reacting to events.

7. **What makes a POST operation retry-safe?**  
   An idempotency key plus server-side request/response deduplication.

8. **Why is cursor pagination preferred for flight search?**  
   It remains stable when results change and avoids offset skip/duplicate issues.

9. **What does `ETag` + `If-Match` prevent?**  
   Lost updates through optimistic concurrency control.

10. **What is HATEOAS?**  
   Hypermedia as the Engine of Application State; responses expose valid next actions.

11. **What is the N+1 problem in GraphQL?**  
   A resolver issues one query per nested item; solve with DataLoader batching.

12. **What is a persisted GraphQL query?**  
   A pre-registered query identified by an ID, reducing arbitrary query risk.

13. **What is GraphQL federation?**  
   Composition of independently owned subgraphs into one unified graph.

14. **Why place an API facade in front of legacy SOAP?**  
   To protect modern consumers from legacy XML contracts and enable gradual modernization.

15. **What is a distributed monolith?**  
   Microservices that are independently deployed but require tightly coupled synchronous calls, losing microservice benefits.

16. **What is a claim-check pattern?**  
   Storing a large payload externally and passing only a reference through the message.

17. **What is the difference between correlation ID and causation ID?**  
   Correlation ID links an entire business flow; causation ID identifies the specific event that caused another event.

18. **What is a hedged request?**  
   Sending a duplicate request after a delay to reduce tail latency.

19. **What is load shedding?**  
   Rejecting or deprioritizing low-value traffic to protect critical operations.

20. **What is graceful degradation?**  
   Maintaining core functionality when a non-critical dependency fails.

21. **What is a consumer-driven contract test?**  
   A test where consumer expectations define the provider’s required contract.

22. **What is API drift?**  
   Divergence between documented contract and actual implementation.

23. **What is a breaking change in an API?**  
   A change that can force existing consumers to modify code/configuration.

24. **What is an API deprecation sunset date?**  
   The date after which an API version is retired.

25. **What is the primary goal of API observability?**  
   Understand behavior, diagnose failures, and improve reliability using logs, metrics, and traces.
