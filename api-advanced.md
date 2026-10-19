# Advanced API Programming — Airports, Flights & Airlines

At an advanced level, API programming is not only about exposing endpoints; it is about designing **contracts, consistency, security, observability, resilience, versioning, and governance** for distributed airline systems. This note assumes you already know REST basics, HTTP methods, JSON, Spring Boot/FastAPI, and GraphQL fundamentals. gRPC uses Protocol Buffers as its IDL and serialization format, and by default runs over HTTP/2, enabling multiplexing and streaming. [grpc](https://grpc.io/docs/what-is-grpc/introduction/)

## 1. Advanced API Design Principles

### Contract-first vs code-first

| Approach | Description | Airline use case |
|---|---|---|
| Contract-first | Define OpenAPI/Protobuf/GraphQL schema before implementation | Airline exposes a stable partner-booking contract |
| Code-first | Generate specification from code | Internal service APIs evolve rapidly |
| Consumer-driven contract | Consumer defines expected provider behavior | Mobile team defines booking-response contract |

**Contract-first is preferred** for public/partner APIs because it enables parallel development, mock servers, SDK generation, compatibility checks, and clear versioning.

### API contract elements

A production-grade airline API contract should specify:

- Resources and operations
- Request/response schemas
- Required/optional fields
- Data types, formats, enums, and constraints
- Authentication and authorization requirements
- Error schema
- Pagination, filtering, sorting
- Rate limits and quotas
- Idempotency rules
- Deprecation policy
- SLA/SLO expectations

### Resource modeling: flights

Avoid modeling every action as a resource. Model **entities, state transitions, and relationships** carefully.

```text
Airline
 ├── Aircraft
 ├── Flight
 │    ├── FlightLeg
 │    ├── SeatMap
 │    └── FlightStatus
 ├── Airport
 ├── Passenger
 ├── Booking
 │    ├── Ticket
 │    ├── SeatAssignment
 │    ├── Baggage
 │    └── Payment
 └── LoyaltyAccount
```

| Resource | Identifier | Example |
|---|---|---|
| Airport | IATA code | `MAA`, `DEL` |
| Flight | Flight number + date | `AI-202/2026-11-15` |
| Booking | Booking reference | `BK-90812` |
| Passenger | Passenger ID | `PAX-77341` |
| Baggage | Bag tag | `AI-882914` |
| Payment | Payment ID | `PAY-552190` |

### URI design: advanced rules

```text
/api/v1/airports/{airportCode}
/api/v1/flights/{flightId}
/api/v1/flights/{flightId}/seats
/api/v1/bookings/{bookingReference}
/api/v1/bookings/{bookingReference}/passengers
/api/v1/bookings/{bookingReference}/baggage
/api/v1/passengers/{passengerId}/loyalty-account
/api/v1/airports/{airportCode}/flights?direction=departure
```

Advanced rules:

- Use **stable business identifiers** where possible, e.g. booking reference instead of internal database ID.
- Keep identifiers opaque; do not expose sequential database IDs publicly.
- Use **sub-resources** for ownership relationships.
- Avoid deep nesting beyond two levels: `/bookings/{id}/payments/{paymentId}` is acceptable; deeper chains become brittle.
- Use query parameters for non-resource concerns: filtering, sorting, pagination, field selection.
- Use **POST on controller resources** only for non-CRUD state transitions: `/bookings/{id}/cancellations`.

### HTTP semantics: advanced

| Concern | Rule | Airline example |
|---|---|---|
| Safe methods | Do not change server state | `GET /flights/AI-202` |
| Idempotent methods | Repeat produces same observable state | `PUT`, `DELETE` |
| POST | Creates resource or triggers non-idempotent action | Create booking |
| PUT | Full replacement; client supplies complete representation | Replace passenger contact details |
| PATCH | Partial update | Update seat preference |
| Conditional requests | Use `ETag`/`If-Match` to prevent lost updates | Two agents edit same booking |
| Content negotiation | `Accept`, `Content-Type` | JSON vs CSV flight report |
| Caching | `Cache-Control`, `ETag`, `Last-Modified` | Airport master data |

### Optimistic concurrency

Use `ETag` and `If-Match` to prevent one agent overwriting another agent’s booking change.

```http
GET /api/v1/bookings/BK-90812

HTTP/1.1 200 OK
ETag: "booking-v17"
```

```http
PATCH /api/v1/bookings/BK-90812
If-Match: "booking-v17"
Content-Type: application/json

{
  "seatPreference": "WINDOW"
}
```

If another agent already changed the booking, server returns:

```http
HTTP/1.1 412 Precondition Failed
```

## 2. Advanced REST Design

### Richardson Maturity Model

| Level | Description | Airline example |
|---:|---|---|
| 0 | Remote procedure calls over HTTP | `POST /flightService` |
| 1 | Resources | `/flights`, `/bookings` |
| 2 | HTTP verbs and status codes | `GET /flights`, `POST /bookings`, `404` |
| 3 | HATEOAS | Response includes links for payment, cancellation, check-in |

Most practical APIs operate at **Level 2**; Level 3 adds discoverability through hypermedia links.

### HATEOAS

**HATEOAS** stands for **Hypermedia as the Engine of Application State**. The API response tells the client what valid next actions are available.

```json
{
  "bookingReference": "BK-90812",
  "status": "CONFIRMED",
  "flight": {
    "flightNumber": "AI-202",
    "from": "MAA",
    "to": "DEL"
  },
  "_links": {
    "self": {
      "href": "/api/v1/bookings/BK-90812"
    },
    "check-in": {
      "href": "/api/v1/bookings/BK-90812/check-in",
      "method": "POST"
    },
    "cancel": {
      "href": "/api/v1/bookings/BK-90812/cancellations",
      "method": "POST"
    },
    "payment": {
      "href": "/api/v1/bookings/BK-90812/payments",
      "method": "POST"
    }
  }
}
```

**Benefit:** If check-in becomes unavailable, the API simply omits the `check-in` link; the mobile app does not need a new release.

### Pagination strategies

| Strategy | Mechanism | Pros | Cons | Airline use |
|---|---|---|---|---|
| Offset pagination | `?page=2&size=20` | Simple | Skips/duplicates on inserts | Airport list |
| Cursor/keyset pagination | `?cursor=eyJpZCI6...&size=20` | Stable for large changing datasets | Harder to jump to page N | Flight-search results |
| Seek pagination | `?after_id=123&limit=20` | Efficient with indexed sort key | Requires deterministic ordering | Booking audit log |
| Page-based with total count | `page`, `size`, `totalElements` | Good for admin UIs | Expensive `COUNT(*)` | Agent booking reports |

**Preferred for high-volume flight search:** cursor/keyset pagination.

```http
GET /api/v1/flights?from=MAA&to=DEL&date=2026-11-15&limit=20&cursor=eyJkZXBhcnR1cmUiOiIwOC0zMCJ9
```

### Filtering, sorting, field selection

```http
GET /api/v1/flights?
  from=MAA&
  to=DEL&
  date=2026-11-15&
  cabin=ECONOMY&
  maxPrice=10000&
  sort=departureTime,-fare&
  fields=flightNumber,fare,departureTime
```

- `sort=departureTime,-fare` means ascending departure time, descending fare.
- `fields` is sparse field selection; GraphQL provides this natively.
- Validate and allowlist filter fields to prevent injection and excessive query complexity.

### Bulk operations

For agent portals, use bulk endpoints carefully.

```http
POST /api/v1/bookings/bulk-status
Content-Type: application/json

{
  "bookingReferences": ["BK-101", "BK-102", "BK-103"],
  "targetStatus": "CANCELLED"
}
```

Response should be **partial-result aware**:

```json
{
  "succeeded": ["BK-101"],
  "failed": [
    {
      "bookingReference": "BK-102",
      "reason": "ALREADY_CANCELLED"
    }
  ]
}
```

Use bulk endpoints only when individual calls create unacceptable overhead; otherwise parallelize client-side calls.

## 3. API Versioning and Compatibility

### Versioning strategies

| Strategy | Example | Pros | Cons |
|---|---|---|---|
| URI versioning | `/v1/flights` | Simple, visible, cache-friendly | URL changes |
| Header versioning | `API-Version: 2026-11-01` | Clean URLs | Less discoverable |
| Query parameter | `?version=1` | Easy testing | Caching complications |
| Media-type versioning | `Accept: application/vnd.airline.v2+json` | REST-pure | Complex for clients |
| Date-based versioning | `API-Version: 2026-11-01` | Clear release calendar | Requires disciplined docs |

**Best practice:** URI major version + date/header for minor additive changes.

```text
/api/v1/flights         # major breaking changes
API-Version: 2026-11-01 # minor behavior/schema evolution
```

### Compatibility rules

| Change | Compatibility |
|---|---|
| Add optional response field | Backward compatible |
| Add optional request field | Usually backward compatible |
| Remove response field | Breaking |
| Rename field | Breaking |
| Change field type | Breaking |
| Add required request field | Breaking |
| Add new enum value | Potentially breaking for strict clients |
| Add new endpoint | Usually compatible |
| Change error semantics | Breaking |

### Deprecation lifecycle

1. Announce deprecation in documentation and response headers.
2. Provide migration guide.
3. Maintain old version during transition window.
4. Return `Deprecation` and `Sunset` headers.
5. Monitor usage per version.
6. Disable after consumer migration.

```http
HTTP/1.1 200 OK
Deprecation: true
Sunset: Wed, 31 Dec 2026 23:59:59 GMT
Link: <https://developer.airline.com/migrate-v1-to-v2>; rel="deprecation"
```

## 4. Advanced Authentication and Authorization

### Authentication vs authorization

| Concept | Question | Airline example |
|---|---|---|
| Authentication | Who are you? | Passenger logs in with OTP/password |
| Authorization | What may you do? | Passenger can cancel only own booking |
| Identity | Unique representation of caller | Passenger ID, agent ID, service account |
| Session | Server-side authenticated state | Legacy agent portal |
| Token | Portable signed/asserted credential | JWT/Opaque token |

### OAuth 2.0 roles

| Role | Airline example |
|---|---|
| Resource owner | Passenger |
| Client | Travel app or airline mobile app |
| Authorization server | Airline identity provider |
| Resource server | Booking/check-in API |
| Client ID | Registered app identifier |
| Redirect URI | App callback URL |
| Scope | `bookings:read`, `bookings:write`, `checkin:create` |

OAuth 2.0 is an authorization framework; OpenID Connect adds authentication/identity on top of OAuth 2.0. The OAuth authorization-code exchange uses an authorization code to obtain an access token. [datatracker.ietf](https://datatracker.ietf.org/doc/html/rfc6749)

### OAuth 2.0 grant types

| Grant | Use case | Airline example |
|---|---|---|
| Authorization Code + PKCE | Mobile/web apps acting for a user | Passenger app accesses bookings |
| Client Credentials | Machine-to-machine | Baggage service calls flight-status service |
| Device Authorization | TV/kiosk/limited-input device | Airport lounge display |
| Refresh Token | Obtain new access token | Long-lived mobile session |
| Implicit | Deprecated | Avoid |
| Resource Owner Password | Deprecated | Avoid |

RFC 9700 guidance deprecates the Implicit and Password grants and mandates PKCE for authorization-code flows. [oauth](https://oauth.net/2/oauth-best-practice/)

### PKCE

**PKCE** stands for **Proof Key for Code Exchange**. It protects authorization-code flows against authorization-code interception and is recommended for all OAuth clients. [oauth](https://oauth.net/2/pkce/)

```text
1. Mobile app creates code_verifier
2. code_challenge = BASE64URL(SHA256(code_verifier))
3. App sends code_challenge to authorization server
4. Passenger authenticates and approves
5. Authorization server returns authorization code
6. App sends code + code_verifier to token endpoint
7. Server verifies SHA256(code_verifier) == code_challenge
8. Server issues access token
```

### JWT advanced concepts

A JWT contains:

```text
header.payload.signature
```

Example payload:

```json
{
  "sub": "PAX-77341",
  "iss": "https://auth.airline.com",
  "aud": "booking-api",
  "exp": 1761000000,
  "iat": 1760996400,
  "scope": "bookings:read bookings:write",
  "tenant": "airline-in",
  "roles": ["PASSENGER"]
}
```

| Claim | Meaning |
|---|---|
| `iss` | Issuer |
| `sub` | Subject/user/service identity |
| `aud` | Intended API audience |
| `exp` | Expiry time |
| `nbf` | Not before |
| `iat` | Issued at |
| `jti` | Unique token ID |
| `scope` | Permissions |
| `roles` | Coarse-grained roles |

**Critical:** JWT signature proves integrity; it does **not** prove that the token has not been revoked. For high-risk actions such as refund or cancellation, validate revocation/status server-side.

### Authorization models

| Model | Description | Airline example |
|---|---|---|
| RBAC | Role-based access control | Agent, supervisor, admin |
| ABAC | Attribute-based access control | Agent can access bookings only for assigned region |
| ReBAC | Relationship-based access control | Passenger can access own booking |
| PBAC | Policy-based access control | Refund allowed only before departure and for refundable fare |
| ACL | Access control list | Explicit user-resource permission list |

**Best airline policy example:**

```text
ALLOW cancel_booking IF
  subject.id == booking.passenger_id
  AND booking.status IN ["CONFIRMED", "CHECKED_IN"]
  AND flight.departure_time > now()
  AND fare_rules.refundable == true
```

### OWASP API Security Top 10

The OWASP API Security Top 10 highlights API-specific risks including broken object-level authorization, broken authentication, unrestricted resource consumption, SSRF, security misconfiguration, and unsafe consumption of third-party APIs. [devguide.owasp](https://devguide.owasp.org/en/07-training-education/07-api-top-ten/)

| Risk | Airline attack example | Mitigation |
|---|---|---|
| API1: Broken Object Level Authorization | Passenger accesses `/bookings/BK-999` belonging to another passenger | Enforce ownership check on every object access |
| API2: Broken Authentication | Weak OTP verification or token validation | MFA, short-lived tokens, secure token validation |
| API3: Broken Object Property Level Authorization | Passenger sends `"loyaltyTier": "PLATINUM"` in profile update | Separate read/write DTOs; allowlist mutable fields |
| API4: Unrestricted Resource Consumption | Massive flight-search queries exhaust service | Rate limits, quotas, timeouts, payload limits |
| API5: Broken Function Level Authorization | Normal user calls admin endpoint `/admin/refunds` | Deny by default; role/policy checks |
| API6: Unrestricted Access to Sensitive Business Flows | Bots hoard discounted seats | CAPTCHA, device fingerprinting, velocity checks |
| API7: SSRF | API fetches attacker-supplied webhook URL | URL allowlist, block private IP ranges |
| API8: Security Misconfiguration | Debug endpoint or verbose stack trace exposed | Hardened configs, no debug in production |
| API9: Improper Inventory Management | Old `/v1` API exposes unpatched endpoint | API inventory, retirement process |
| API10: Unsafe Consumption of APIs | Airline trusts compromised partner API response | Validate/schema-check third-party data |

OWASP specifically notes that object-level authorization checks should be applied in every function accessing data using a user-supplied identifier. [owasp](https://owasp.org/projects/api-security-project)

## 5. Rate Limiting, Quotas and Throttling

| Term | Meaning | Airline example |
|---|---|---|
| Rate limit | Maximum requests in a time window | 100 flight searches/minute |
| Quota | Total allowance over longer period | 1 million API calls/month |
| Throttling | Slowing/rejecting excess requests | Delay noncritical status polling |
| Burst limit | Short-term spike allowance | 20 requests/second |
| Token bucket | Tokens refill at a rate; request consumes token | Smooth traffic control |
| Leaky bucket | Requests processed at constant outflow rate | Smooths bursts |
| Fixed window | Count requests in fixed interval | Simple but boundary burst issue |
| Sliding window | Rolling time window count | More accurate |
| Concurrent limit | Maximum simultaneous requests | 50 concurrent booking calls |
| Backpressure | Signal producer to slow down | Kafka consumer lag |

### Token bucket example

```python
import time

class TokenBucket:
    def __init__(self, capacity: int, refill_per_second: float):
        self.capacity = capacity
        self.tokens = capacity
        self.refill_per_second = refill_per_second
        self.last_refill = time.monotonic()

    def allow(self) -> bool:
        now = time.monotonic()
        self.tokens = min(
            self.capacity,
            self.tokens + (now - self.last_refill) * self.refill_per_second
        )
        self.last_refill = now

        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False
```

### Rate-limit headers

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 12
RateLimit-Limit: 100
RateLimit-Remaining: 0
RateLimit-Reset: 12
```

**Design rule:** Apply stricter limits to expensive endpoints such as fare search, and relaxed limits to cheap cached endpoints such as airport lookup.

## 6. Idempotency and Distributed Consistency

### Idempotency

An operation is **idempotent** if performing it multiple times has the same effect as performing it once.

| Operation | Idempotent? | Reason |
|---|---:|---|
| `GET /flights/AI-202` | Yes | Read-only |
| `PUT /passengers/PAX-1` | Yes | Full replacement |
| `DELETE /bookings/BK-1` | Yes | Repeated delete leads to same final state |
| `POST /bookings` | No by default | May create duplicate bookings |
| `POST /payments` | No by default | May double-charge |
| `POST /bookings with Idempotency-Key` | Yes | Server recognizes repeated key |

### Idempotency key flow

```text
Client generates UUID idempotency key
        |
        v
POST /bookings
Idempotency-Key: 7f9c...
        |
        v
API checks key store
        |
        +-- Key exists -> return stored response
        |
        +-- Key new -> process booking, store response
```

```http
POST /api/v1/bookings
Idempotency-Key: 9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d
Content-Type: application/json

{
  "flightId": "AI-202",
  "passengerId": "PAX-77341",
  "seatClass": "ECONOMY"
}
```

**Rules:**
- Key should be unique per logical operation.
- Store key, request fingerprint, response, and expiry.
- Return `409 Conflict` if same key is reused with a different payload.
- Retain keys long enough to cover retries and network timeouts.

### Distributed transactions

A booking may involve booking service, inventory service, payment service, and notification service. A single ACID transaction across all services is usually impossible.

| Pattern | Description | Airline example |
|---|---|---|
| Saga | Sequence of local transactions with compensating actions | Reserve seat -> charge payment -> confirm booking; refund if payment fails |
| Orchestration | Central coordinator directs steps | Booking orchestrator |
| Choreography | Services react to events | Booking-created event triggers payment/notification |
| Outbox pattern | Write business event and outbox row atomically; relay publishes later | Ensure booking-created event is not lost |
| Two-phase commit | Blocking distributed transaction protocol | Rare in microservices |
| TCC | Try-Confirm/Cancel | Reserve, confirm, or release seat |

### Saga example

```text
1. Booking Service: CREATE_BOOKING (PENDING)
2. Inventory Service: RESERVE_SEAT
3. Payment Service: AUTHORIZE_PAYMENT
4. Booking Service: CONFIRM_BOOKING

Failure after payment:
Compensation:
1. Payment Service: REFUND_PAYMENT
2. Inventory Service: RELEASE_SEAT
3. Booking Service: CANCEL_BOOKING
```

### Outbox pattern

```python
# In same DB transaction as booking creation
booking = create_booking(request)
outbox = OutboxEvent(
    event_type="BOOKING_CREATED",
    payload=to_json(booking),
    status="PENDING"
)
save(booking)
save(outbox)
```

A separate relay polls the outbox and publishes `BOOKING_CREATED` to Kafka. This avoids the inconsistency where the database commit succeeds but the Kafka publish fails.

## 7. Event-Driven API Architectures

### Synchronous vs asynchronous

| Aspect | REST/gRPC request-response | Event-driven |
|---|---|---|
| Coupling | Caller knows provider | Producer/consumer decoupled |
| Response | Immediate | Usually delayed/eventual |
| Failure handling | Immediate error | Retries, DLQ, replay |
| Use case | Check seat availability | Booking-created, payment-completed |
| Consistency | Stronger within call | Eventual consistency |

### Airline event catalog

| Event | Producer | Consumers |
|---|---|---|
| `FlightScheduled` | Flight management | Search, pricing, airport ops |
| `FlightDelayed` | Operations | Passenger app, notifications, rebooking |
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
  "eventVersion": "1.0",
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

Important fields:
- `eventId`: Unique event identity.
- `correlationId`: Links all events for one original request.
- `causationId`: Identifies event that caused this event.
- `eventVersion`: Supports schema evolution.

### Delivery semantics

| Semantics | Meaning | Airline implication |
|---|---|---|
| At-most-once | Message may be lost | Not suitable for payment events |
| At-least-once | Message may be duplicated | Consumers must be idempotent |
| Exactly-once | No loss and no duplication | Hard; often effective exactly-once via transactional processing/idempotency |

**Design assumption:** Build consumers as idempotent, because most real systems provide at-least-once delivery.

### Consumer patterns

| Pattern | Purpose |
|---|---|
| Competing consumers | Scale processing across instances |
| Consumer groups | Independent processing per application |
| Dead-letter queue | Store repeatedly failing messages |
| Retry with backoff | Handle transient failures |
| Poison-pill handling | Isolate malformed messages |
| Event sourcing | Store state as immutable events |
| CQRS | Separate write and read models |
| Materialized view | Precomputed read model for fast queries |

**Airline CQRS example:**
- Write model: bookings, payments, seat inventory.
- Read model: flight-search results, airport departure board, agent dashboard.

## 8. gRPC and Protocol Buffers

**gRPC** is a high-performance RPC framework. It commonly uses **Protocol Buffers** as its Interface Definition Language and serialization mechanism, and HTTP/2 as transport. [grpc](https://grpc.io/docs/what-is-grpc/introduction/)

### Why gRPC

| Feature | Benefit |
|---|---|
| Protobuf | Compact binary serialization |
| HTTP/2 | Multiplexing, header compression, flow control |
| Streaming | Unary, server-streaming, client-streaming, bidirectional |
| Code generation | Client/server stubs in many languages |
| Strong typing | Contract enforced through `.proto` schema |
| Deadlines | Client-specified timeout propagation |
| Interceptors | Cross-cutting auth/logging/metrics |

gRPC’s HTTP/2 foundation supports bidirectional streaming, flow control, header compression, and multiplexing over one connection. [developer.android](https://developer.android.com/guide/topics/connectivity/grpc)

### Proto example

```proto
syntax = "proto3";

package airline.v1;

service FlightService {
  rpc GetFlightStatus (FlightStatusRequest) returns (FlightStatusResponse);
  rpc StreamFlightUpdates (FlightUpdatesRequest) returns (stream FlightUpdate);
}

message FlightStatusRequest {
  string flight_number = 1;
  string flight_date = 2;
}

message FlightStatusResponse {
  string flight_number = 1;
  string status = 2;
  string gate = 3;
  int32 delay_minutes = 4;
}

message FlightUpdatesRequest {
  string flight_number = 1;
}

message FlightUpdate {
  string status = 1;
  string gate = 2;
  int32 delay_minutes = 3;
}
```

### gRPC communication patterns

| Pattern | Direction | Airline example |
|---|---|---|
| Unary | One request, one response | Get flight status |
| Server streaming | One request, many responses | Live flight updates |
| Client streaming | Many requests, one response | Upload baggage-scan batches |
| Bidirectional streaming | Many requests and responses | Real-time airport operations chat/telemetry |

### gRPC error codes

| Code | Meaning | Airline example |
|---|---|---|
| `OK` | Success | Status returned |
| `INVALID_ARGUMENT` | Bad input | Invalid flight date |
| `NOT_FOUND` | Missing resource | Unknown booking |
| `PERMISSION_DENIED` | Authorization failure | Agent cannot refund |
| `UNAUTHENTICATED` | Missing/invalid credentials | Expired token |
| `RESOURCE_EXHAUSTED` | Quota/rate limit | Too many searches |
| `FAILED_PRECONDITION` | System not in required state | Check-in before seat assignment |
| `ABORTED` | Concurrency conflict | Seat update conflict |
| `UNAVAILABLE` | Temporary service issue | Flight service down |
| `DEADLINE_EXCEEDED` | Timeout | Fare service slow |
| `INTERNAL` | Server error | Unexpected exception |

### gRPC vs REST

| Feature | gRPC | REST |
|---|---|---|
| Contract | `.proto` | OpenAPI |
| Format | Protobuf binary | JSON/XML/text |
| Transport | HTTP/2 | HTTP/1.1 or HTTP/2 |
| Browser support | Requires gRPC-Web/proxy | Native |
| Streaming | First-class | WebSocket/SSE needed |
| Caching | Harder | HTTP caching easier |
| Best for | Internal service-to-service | Public/web/mobile APIs |

## 9. GraphQL at Scale

### Advanced GraphQL concerns

| Concern | Problem | Airline solution |
|---|---|---|
| N+1 problem | One resolver call per nested item | DataLoader batching |
| Query depth | Deep nested query overwhelms server | Depth limiting |
| Query complexity | Expensive computed fields | Complexity scoring |
| Persisted queries | Arbitrary queries from clients | Store approved query IDs |
| Introspection | Schema exposure | Disable in production if not needed |
| Caching | POST-based GraphQL complicates HTTP cache | APQ, response caching, CDN edge caching |
| Federation | Multiple teams own graph | Apollo Federation / schema composition |

### N+1 problem

For a query returning 20 flights, each with an `airport` field, naïve resolvers may issue 20 airport queries.

**Solution: DataLoader**

```python
from graphql import GraphQLError

async def airports_batch_loader(airport_codes):
    airports = await airport_repo.find_by_codes(airport_codes)
    airport_map = {a.iata_code: a for a in airports}
    return [airport_map.get(code) for code in airport_codes]
```

The resolver collects all requested airport codes in one execution cycle, then issues one batched query.

### GraphQL federation

In a federated graph, each domain owns its schema:

| Subgraph | Owns |
|---|---|
| Flight subgraph | `Flight`, `Airline` |
| Airport subgraph | `Airport`, terminal/gate data |
| Booking subgraph | `Booking`, `Passenger` |
| Loyalty subgraph | Loyalty account and miles |

```graphql
type Flight @key(fields: "flightId") {
  flightId: ID!
  flightNumber: String!
  departureAirport: Airport
}

type Airport @key(fields: "iataCode") {
  iataCode: ID!
  name: String!
  city: String!
}
```

The gateway composes the subgraphs into one graph for clients.

### GraphQL security rules

- Disable introspection in production unless required.
- Set maximum query depth, e.g. 8.
- Set maximum aliases and maximum query complexity.
- Use persisted/pre-registered queries for public mobile apps.
- Apply field-level authorization.
- Never expose internal errors or stack traces.
- Rate-limit by API key, user, IP, and operation complexity.

## 10. API Gateway and Service Mesh

### API gateway

An API gateway is the controlled entry point for external clients.

| Function | Airline example |
|---|---|
| Routing | `/flights/**` to flight service |
| Authentication | Validate JWT/API key |
| Authorization | Enforce scopes |
| Rate limiting | Limit partner search requests |
| Caching | Cache airport master data |
| Transformation | Convert legacy XML to JSON |
| Observability | Logs, traces, metrics |
| Canary routing | Send 5% traffic to v2 |
| WAF | Block common attacks |

### Service mesh

A service mesh manages **service-to-service** communication, separate from the API gateway’s external traffic role.

| Feature | Purpose |
|---|---|
| mTLS | Encrypted, authenticated service traffic |
| Traffic management | Retries, timeouts, circuit breaking |
| Observability | Distributed tracing and metrics |
| Policy enforcement | Authorization at network/service layer |
| Canary deployments | Gradual traffic shifting |
| Fault injection | Test resilience |

**Rule of thumb:** API gateway = north-south traffic; service mesh = east-west traffic.

## 11. Resilience Patterns

| Pattern | Purpose | Airline example |
|---|---|---|
| Timeout | Prevent indefinite waiting | Fare service must respond in 800 ms |
| Retry | Handle transient failure | Retry `503` from payment gateway |
| Exponential backoff | Avoid hammering failing service | 100 ms, 200 ms, 400 ms |
| Jitter | Prevent synchronized retries | Add random delay |
| Circuit breaker | Stop calls to failing dependency | Stop payment calls after repeated failures |
| Bulkhead | Isolate resource pools | Separate connection pools for search and booking |
| Fallback | Provide degraded response | Show last-known flight status |
| Cache-aside | Read cache first, then DB | Airport details |
| Request hedging | Send duplicate request after delay | Reduce tail latency |
| Load shedding | Reject low-priority traffic under overload | Drop anonymous flight searches first |
| Graceful degradation | Maintain core functionality | Booking works even if loyalty service fails |

### Circuit breaker states

| State | Meaning |
|---|---|
| Closed | Requests pass normally |
| Open | Requests fail immediately; dependency considered unhealthy |
| Half-open | Limited test requests check recovery |

```text
Payment failures > threshold
        |
        v
Circuit OPEN for 30 seconds
        |
        v
HALF-OPEN test request
        |
        +-- Success -> CLOSED
        +-- Failure -> OPEN
```

### Retry rules

Retry only when:

- Error is transient: timeout, `503`, connection reset.
- Operation is idempotent or uses an idempotency key.
- Retry budget is configured.
- Backoff and jitter are used.

Do **not** blindly retry `POST /payments` without an idempotency key.

## 12. Caching Strategies

| Strategy | Description | Airline example |
|---|---|---|
| Client-side caching | Browser/app cache | Airport list |
| CDN caching | Edge cache | Static fare rules, airport images |
| API gateway cache | Gateway caches response | Popular flight-search route |
| Redis cache | Distributed in-memory cache | Seat availability |
| Cache-aside | App checks cache, then DB | Airport details |
| Read-through | Cache loads missing data from DB | Reference data |
| Write-through | Write cache and DB together | Critical config |
| Write-behind | Async DB write after cache write | Risky for payments |
| TTL | Time-to-live expiry | Flight status cached 15 seconds |

### Cache invalidation

| Method | Description |
|---|---|
| TTL expiry | Simple but may serve stale data |
| Event-based invalidation | Invalidate on `FlightStatusChanged` |
| Versioned keys | `flight:AI-202:v17` |
| Purge on write | Delete/update cache after DB write |
| Short TTL + stale-while-revalidate | Balance freshness and latency |

**Never cache** passenger PII, payment data, or personalized booking details in a shared public cache without strict key isolation.

## 13. Observability

### Three pillars

| Pillar | Purpose | Airline example |
|---|---|---|
| Logs | Discrete event records | Booking failed due to seat conflict |
| Metrics | Numeric time-series data | Booking API p99 latency |
| Traces | Request path across services | Mobile -> gateway -> booking -> payment |

### Golden signals

| Signal | Meaning | Airline example |
|---|---|---|
| Latency | Time to respond | Flight search p95 < 300 ms |
| Traffic | Request rate | 5,000 searches/second |
| Errors | Failed requests | 0.1% booking error rate |
| Saturation | Resource usage | DB connections at 80% |

### Useful API metrics

- Request rate by endpoint/status
- Error rate by endpoint
- p50/p95/p99 latency
- Upstream dependency latency
- Cache hit ratio
- Rate-limit rejections
- Idempotency-key replay count
- Saga compensation rate
- Kafka consumer lag
- Queue depth/DLQ count
- Certificate expiry
- API version usage

### Correlation and trace propagation

```http
X-Request-Id: req-77123
X-Correlation-Id: booking-flow-8891
traceparent: 00-4bf92f...-00f067...-01
```

Propagate these headers through gateway, booking, payment, inventory, and notification services.

## 14. API Performance Engineering

### Optimization techniques

| Technique | Description | Airline example |
|---|---|---|
| Pagination | Limit result size | 20 flights per page |
| Field projection | Return only needed fields | Mobile gets fare + time only |
| Compression | Gzip/Brotli | Compress large flight lists |
| HTTP/2 | Multiplex requests | Mobile app parallel API calls |
| Connection pooling | Reuse DB/HTTP connections | Booking service DB pool |
| Async I/O | Non-blocking operations | FastAPI/Netty |
| Batch APIs | Combine multiple calls | Fetch 20 airport details in one call |
| Read replicas | Scale read traffic | Flight-search DB replicas |
| Materialized views | Precompute expensive queries | Departure-board data |
| Edge caching | Serve from nearest location | Airport reference data |
| Database indexing | Speed up filtered queries | `(from_airport, to_airport, departure_date)` |

### SQL index example

```sql
CREATE INDEX idx_flight_search
ON flights (from_airport, to_airport, departure_date, departure_time);
```

For a query filtering by route and date, this composite index can avoid a full table scan.

### Latency budget example

```text
Total flight-search SLO: 300 ms p95

Gateway                10 ms
Authentication          5 ms
Flight Search Service  80 ms
Fare Pricing Service   70 ms
Seat Inventory Service 60 ms
Cache/DB access        40 ms
Serialization           15 ms
Network overhead        20 ms
```

Set downstream timeouts below the remaining end-to-end budget.

## 15. API Testing at Scale

| Test type | Purpose | Tools |
|---|---|---|
| Contract testing | Provider/consumer compatibility | Pact, Spring Cloud Contract |
| Schema validation | Enforce request/response schema | jsonschema, OpenAPI validators |
| Integration testing | Test real/mocked dependencies | Testcontainers, pytest |
| Chaos testing | Validate failure handling | Chaos Mesh, Litmus |
| Load testing | Measure throughput/latency | k6, JMeter, Locust |
| Soak testing | Detect memory/connection leaks | k6/JMeter long runs |
| Fuzzing | Find malformed-input failures | RESTler, Schemathesis |
| Security testing | Find auth/authz flaws | OWASP ZAP, Burp Suite |
| Penetration testing | Simulate attacker behavior | Manual/automated security testing |

### Contract-test example

Consumer expectation:

```json
{
  "bookingReference": "BK-90812",
  "status": "CONFIRMED",
  "fare": 7500
}
```

Provider must satisfy this contract before deployment. If the provider removes `fare`, the consumer build fails.

## 16. API Governance

| Area | Practice |
|---|---|
| API catalog | Register every API, owner, version, environment |
| Standards | Naming, errors, pagination, security, logging |
| Design review | Review contracts before implementation |
| Security review | Threat-model sensitive operations |
| Version policy | Define breaking-change and deprecation rules |
| Lifecycle | Design -> build -> test -> publish -> monitor -> deprecate |
| Access management | Partner onboarding/offboarding |
| Compliance | PCI-DSS for payments, GDPR/DPDP for personal data |
| Audit | Retain booking/payment/change logs |
| Documentation | Always current, executable, and discoverable |

### API lifecycle

```text
Design -> Review -> Mock -> Develop -> Test
      -> Secure -> Publish -> Monitor
      -> Version -> Deprecate -> Retire
```

## 17. Python-Centric Advanced Examples

### FastAPI with idempotency and dependency injection

```python
import uuid
from fastapi import FastAPI, Header, HTTPException, status
from pydantic import BaseModel, Field

app = FastAPI(title="Airline Booking API")

class BookingRequest(BaseModel):
    flight_id: str
    passenger_id: str
    seat_class: str = Field(pattern="^(ECONOMY|BUSINESS|FIRST)$")

class BookingResponse(BaseModel):
    booking_reference: str
    status: str

@app.post(
    "/api/v1/bookings",
    response_model=BookingResponse,
    status_code=status.HTTP_201_CREATED,
)
async def create_booking(
    request: BookingRequest,
    idempotency_key: str = Header(...),
):
    existing = await booking_store.get_by_idempotency_key(idempotency_key)
    if existing:
        return existing

    booking = await booking_service.create_booking(request)

    response = BookingResponse(
        booking_reference=booking.reference,
        status=booking.status,
    )
    await booking_store.save_idempotency_response(
        idempotency_key=idempotency_key,
        response=response,
    )
    return response
```

### Token-bucket rate limiter with Redis

```python
import time
import redis

r = redis.Redis()

def allow_request(client_id: str, capacity=100, refill_rate=2.0) -> bool:
    key = f"ratelimit:{client_id}"
    now = time.time()

    pipe = r.pipeline()
    pipe.hgetall(key)
    _, data = pipe.execute()

    tokens = float(data.get(b"tokens", capacity))
    last = float(data.get(b"last", now))

    tokens = min(capacity, tokens + (now - last) * refill_rate)

    if tokens < 1:
        pipe.hset(key, mapping={"tokens": tokens, "last": now})
        pipe.execute()
        return False

    tokens -= 1
    pipe.hset(key, mapping={"tokens": tokens, "last": now})
    pipe.execute()
    return True
```

### Async consumer with idempotent processing

```python
import json
from dataclasses import dataclass

@dataclass
class PaymentCompleted:
    booking_reference: str
    payment_id: str
    amount: float

async def handle_payment_completed(event: dict):
    payment_id = event["paymentId"]

    if await processed_events.exists(payment_id):
        return  # Idempotent replay handling

    await booking_service.confirm_booking(event["bookingReference"])
    await processed_events.mark_processed(payment_id)
```

## 18. Maximum-Difficulty One-Liners

- **HATEOAS** = Hypermedia as the Engine of Application State.
- **Idempotency** means repeating an operation produces the same final state.
- `PUT`, `DELETE`, `GET`, `HEAD`, and `OPTIONS` are idempotent; `POST` is not inherently idempotent.
- `ETag` + `If-Match` enables optimistic concurrency control.
- **Cursor pagination** is preferable for high-volume changing datasets.
- **OAuth 2.0** is authorization; **OpenID Connect** adds authentication.
- **PKCE** protects authorization-code flows from code-interception attacks. [oauth](https://oauth.net/2/pkce/)
- OAuth best practice deprecates Implicit and Password grants and mandates PKCE for authorization-code flows. [oauth](https://oauth.net/2/oauth-best-practice/)
- **JWT signature** ensures integrity, not revocation.
- **JTI** is a JWT’s unique token identifier.
- **RBAC** uses roles; **ABAC** uses attributes; **ReBAC** uses relationships.
- **BOLA** = Broken Object Level Authorization.
- OWASP API1:2023 is **Broken Object Level Authorization**. [devguide.owasp](https://devguide.owasp.org/en/07-training-education/07-api-top-ten/)
- **SSRF** can occur when an API fetches a user-supplied URL without validation. [devguide.owasp](https://devguide.owasp.org/en/07-training-education/07-api-top-ten/)
- **Token bucket** allows controlled bursts; **leaky bucket** smooths output rate.
- **Saga** maintains data consistency across microservices using local transactions and compensations.
- **Outbox pattern** reliably publishes domain events after a database transaction.
- **At-least-once delivery** requires idempotent consumers.
- **CQRS** separates write and read models.
- **Event sourcing** stores state as an immutable sequence of events.
- **gRPC** uses Protobuf and HTTP/2 by default. [grpc](https://grpc.io/docs/what-is-grpc/introduction/)
- gRPC supports unary, server-streaming, client-streaming, and bidirectional-streaming calls. [grpc](https://grpc.io/)
- **Deadline propagation** carries timeout information across gRPC calls.
- **API gateway** handles external north-south traffic; **service mesh** handles internal east-west traffic.
- **Circuit breaker** prevents repeated calls to an unhealthy dependency.
- **Bulkhead** isolates resource pools to prevent cascading failure.
- **Backpressure** tells producers to slow down when consumers cannot keep up.
- **Golden signals** are latency, traffic, errors, and saturation.
- **Correlation ID** links all events/requests in one business flow.
- **p99 latency** is the response time below which 99% of requests complete.
- **Cardinality** is the number of unique label combinations in metrics.
- **Contract testing** verifies provider-consumer compatibility.
- **Chaos engineering** deliberately injects failures to validate resilience.
- **Federation** composes multiple GraphQL subgraphs into one graph.
- **DataLoader** solves GraphQL N+1 resolver problems through batching/caching.
- **Persisted queries** restrict clients to pre-approved GraphQL operations.
- **API inventory** prevents forgotten, deprecated, or shadow APIs.
- **Unsafe consumption of APIs** means trusting third-party API responses without validation. [devguide.owasp](https://devguide.owasp.org/en/07-training-education/07-api-top-ten/)

## 19. Maximum-Level Probable Questions

1. **Which REST maturity level includes hypermedia links?**  
   Level 3 — HATEOAS.

2. **Which pattern prevents duplicate booking creation during retries?**  
   Idempotency key.

3. **Which pattern ensures a domain event is published reliably after a DB transaction?**  
   Transactional outbox pattern.

4. **Which distributed-data pattern uses compensating transactions?**  
   Saga.

5. **Which OAuth mechanism protects authorization-code interception?**  
   PKCE. [rfc-editor](https://www.rfc-editor.org/info/rfc7636/)

6. **Which OAuth grants are deprecated by modern best practice?**  
   Implicit and Resource Owner Password Credentials grants. [oauth](https://oauth.net/2/oauth-best-practice/)

7. **Which OWASP API risk involves accessing another passenger’s booking by changing an ID?**  
   Broken Object Level Authorization. [devguide.owasp](https://devguide.owasp.org/en/07-training-education/07-api-top-ten/)

8. **Which OWASP API risk involves excessive flight-search requests causing denial of service?**  
   Unrestricted Resource Consumption. [devguide.owasp](https://devguide.owasp.org/en/07-training-education/07-api-top-ten/)

9. **Which rate-limiting algorithm permits controlled bursts?**  
   Token bucket.

10. **Which rate-limiting algorithm smooths request processing at a constant rate?**  
   Leaky bucket.

11. **Which gRPC pattern sends one request and receives many responses?**  
   Server streaming.

12. **Which gRPC pattern is suitable for live flight-status updates?**  
   Server streaming or bidirectional streaming.

13. **Which serialization format does gRPC commonly use?**  
   Protocol Buffers. [grpc](https://grpc.io/docs/what-is-grpc/introduction/)

14. **Which transport protocol does gRPC use by default?**  
   HTTP/2. [developer.android](https://developer.android.com/guide/topics/connectivity/grpc)

15. **Which GraphQL issue occurs when each nested field triggers a separate database query?**  
   N+1 problem.

16. **Which GraphQL mechanism batches resolver calls?**  
   DataLoader.

17. **Which GraphQL architecture composes domain-specific schemas?**  
   Federation.

18. **Which component handles external client traffic in microservices?**  
   API gateway.

19. **Which component manages internal service-to-service mTLS and traffic policies?**  
   Service mesh.

20. **Which resilience pattern stops calls after repeated dependency failures?**  
   Circuit breaker.

21. **Which resilience pattern isolates database/connection pools?**  
   Bulkhead.

22. **Which event-delivery guarantee requires consumers to handle duplicates?**  
   At-least-once.

23. **Which architecture separates booking writes from departure-board reads?**  
   CQRS.

24. **Which metric measures the response time below which 99% requests finish?**  
   p99 latency.

25. **Which header prevents concurrent overwrite of a booking?**  
   `If-Match` with `ETag`.
