# Module 3: API Programming, Microservices & Cloud-Native Integration
**Syllabus Coverage:** Part-B, Section 3 (Intro, i, ii, iii, iv) — Designing, developing, and consuming APIs using REST, SOAP, GraphQL, etc.; API framework development; Modular development & abstraction; Microservices architecture & modern cloud-native applications; Cross-platform integration (mobile, web, desktop).

## 3.1 API Protocols & Architectural Styles (REST, SOAP, GraphQL, gRPC, WebSockets)

### Master Comparison Matrix: REST vs. SOAP vs. GraphQL vs. gRPC
| Feature | **REST (Representational State Transfer)** | **SOAP (Simple Object Access Protocol)** | **GraphQL** | **gRPC (Google Remote Procedure Call)** |
| :--- | :--- | :--- | :--- | :--- |
| **Classification** | **Architectural Style** (defined by **Roy Fielding**, 2000) | **Strict Messaging Protocol** (W3C standard) | **Query Language for APIs** & Server-side Runtime (Meta, 2015) | **High-Performance RPC Framework** (CNCF / Google) |
| **Data Format** | **JSON** (most common), XML, HTML, YAML, Plain Text | **XML ONLY** (strictly structured SOAP Envelope) | **JSON** (response mirrors exact query shape) | **Protocol Buffers (Protobuf)** (compact binary serialization) |
| **Transport Protocol** | **HTTP / HTTPS** (HTTP/1.1, HTTP/2, HTTP/3) | Transport-independent (**HTTP, SMTP, TCP, JMS, MQ**) | **HTTP / HTTPS** (Queries/Mutations) & **WebSockets** (Subscriptions) | **HTTP/2 ONLY** (supports multiplexing, bidirectional streaming) |
| **Endpoint Structure** | **Multiple URLs** representing **Resources** (nouns, e.g., `/users/42/orders`) | Typically a single service endpoint mapped via **WSDL** operations | **Single Endpoint** (typically **`POST /graphql`**) | Service & Method RPC definitions in **`.proto`** IDL files |
| **Data Fetching Efficiency** | Prone to **Over-fetching** (too many fields) & **Under-fetching** ($N+1$ round trips) | Fixed rigid XML contract defined by XSD/WSDL | **Exact-fetching** (client specifies exact fields needed in 1 request) | Strongly typed binary contract; ultra-low latency |
| **Built-in Standards** | Relies on HTTP/TLS, OAuth 2.0, OpenAPI | **WS-* Specifications** (**WS-Security**, **WS-AtomicTransaction**, **WS-ReliableMessaging**) | **SDL (Schema Definition Language)** & **Introspection** (`__schema`) | Protobuf schema + native TLS/ALTS & interceptors |
| **HTTP Caching** | **Native HTTP Caching** (`GET` responses cached via `ETag`, `Cache-Control`) | Difficult (uses `POST` with XML payloads) | No native HTTP URL caching (requires normalized client cache like Apollo/Relay or persisted queries) | No standard HTTP caching (RPC-oriented) |
| **Best Use Case** | Public web APIs, CRUD services, resource-oriented architectures | Enterprise banking/payment gateways, legacy ERP, ACID distributed transactions | Mobile/Web apps with complex nested relational graphs & variable bandwidth | Internal **Microservice-to-Microservice** (East-West) low-latency communication |

### 1. REST (Representational State Transfer) Deep Dive

#### Roy Fielding's 6 Architectural Constraints of REST (Frequent MCQ/MSQ)
1. **Client-Server Separation:** Decouples UI/client concerns from data storage/server concerns, improving portability and scalability.
2. **Statelessness (Mandatory):** Every client request must contain **all information necessary** to process it (e.g., Bearer token, parameters). The server stores **zero client session state** between requests.
3. **Cacheability (Mandatory):** Responses must explicitly or implicitly define themselves as cacheable or non-cacheable (`Cache-Control`, `Expires`, `ETag`, `Last-Modified`).
4. **Uniform Interface (Mandatory — 4 Sub-constraints):**
   * **Identification of resources** (via URIs, e.g., `/api/v1/employees/105`).
   * **Manipulation of resources through representations** (JSON/XML payload + `Content-Type`).
   * **Self-descriptive messages** (standard HTTP methods, headers, media types).
   * **HATEOAS (Hypermedia as the Engine of Application State):** Responses include hypermedia links (`_links`) telling the client what state transitions/actions are dynamically available next.
5. **Layered System:** Client cannot tell whether it is connected directly to the origin server or an intermediate proxy, CDN, API Gateway, or load balancer.
6. **Code-on-Demand (The ONLY Optional Constraint):** Server can temporarily extend client functionality by transferring executable code (e.g., JavaScript applets/scripts).

#### Richardson Maturity Model (RMM — 4 Levels of REST Maturity)
* **Level 0 (The Swamp of POX):** Single URI using a single HTTP method (usually `POST`) to send Plain Old XML/JSON RPC payloads (e.g., SOAP/XML-RPC).
* **Level 1 (Resources):** Introduces distinct **Resource URIs** (`/users/1`, `/orders/5`), but still tunnels operations through a single HTTP verb.
* **Level 2 (HTTP Verbs & Status Codes):** Uses standard **HTTP Methods** (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) and meaningful **HTTP Status Codes** (`200`, `201`, `404`, etc.).
* **Level 3 (Hypermedia Controls — HATEOAS):** True REST; responses include **HATEOAS** hypermedia links for dynamic service discovery.

#### HTTP Methods: Safety & Idempotency Matrix (High-Yield Exam Table)
* **Safe Method:** Does **not** modify resource state on the server (read-only semantics).
* **Idempotent Method:** Making **multiple identical requests** has the **exact same effect on server state** as making a single request ($f(f(x)) = f(x)$).

| HTTP Method | CRUD Operation | **Safe?** | **Idempotent?** | Request Body? | Response Body? | Cacheable? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`GET`** | Read / Retrieve resource(s) | **Yes** | **Yes** | No | Yes | **Yes** |
| **`HEAD`** | Retrieve headers only (no body) | **Yes** | **Yes** | No | **No** | **Yes** |
| **`OPTIONS`** | Discover allowed methods / CORS preflight | **Yes** | **Yes** | No | Optional | No |
| **`TRACE`** | Diagnostic loopback test of request path | **Yes** | **Yes** | No | Yes | No |
| **`PUT`** | **Replace** entire resource at target URI (or create if URI known) | No | **Yes** | Yes | Optional | No |
| **`DELETE`** | Delete resource at target URI | No | **Yes** | Optional | Optional | No |
| **`POST`** | **Create** subordinate resource / trigger processing | No | **No** | Yes | Yes | Only if freshness headers set |
| **`PATCH`** | **Partial modification** of resource fields | No | **No** (can be designed idempotent, but **not guaranteed** by HTTP spec, e.g., `{"op": "increment"}`) | Yes | Yes | No |

#### Essential HTTP Status Codes for API Exams
* **`1xx` Informational:**
  * `100 Continue`, `101 Switching Protocols` (used during **WebSocket** upgrade handshake).
* **`2xx` Success:**
  * **`200 OK`:** Standard success for `GET`, `PUT`, `PATCH`, or `DELETE` with body.
  * **`201 Created`:** Resource successfully created via `POST` (returns new resource URI in **`Location`** header).
  * **`202 Accepted`:** Request accepted for **asynchronous** background processing (not yet completed).
  * **`204 No Content`:** Request succeeded (`DELETE` or `PUT`), intentionally returns **empty response body**.
  * **`206 Partial Content`:** Range request (`Range: bytes=0-1023`) for streaming/resumable downloads.
* **`3xx` Redirection:**
  * **`301 Moved Permanently`** vs **`302 Found`** (Temporary redirect; historic browsers may change `POST` to `GET`).
  * **`307 Temporary Redirect`** & **`308 Permanent Redirect`:** Strictly **forbid** changing the HTTP method (`POST` remains `POST`).
  * **`304 Not Modified`:** Conditional `GET` (`If-None-Match` / `ETag` or `If-Modified-Since`); tells client to use its cached copy.
* **`4xx` Client Errors:**
  * **`400 Bad Request`:** Malformed JSON syntax, invalid query parameter, or request schema failure.
  * **`401 Unauthorized`:** **Unauthenticated** — missing or invalid credentials/token (returns `WWW-Authenticate` header).
  * **`403 Forbidden`:** **Unauthorized** — server knows the user's identity, but the user lacks permission/role to access the resource.
  * **`404 Not Found`:** Resource URI does not exist.
  * **`405 Method Not Allowed`:** HTTP verb not supported on that URI (must return **`Allow`** header listing valid verbs, e.g., `Allow: GET, HEAD`).
  * **`406 Not Acceptable`:** Server cannot produce a response matching the client's **`Accept`** content-negotiation header.
  * **`409 Conflict`:** State conflict (e.g., duplicate unique key or optimistic locking version mismatch).
  * **`412 Precondition Failed`:** Conditional update (`If-Match` with `ETag`) failed — prevents the **"Lost Update" concurrency problem**.
  * **`415 Unsupported Media Type`:** Client sent payload in an unsupported format (`Content-Type` header rejected).
  * **`422 Unprocessable Entity (Content)`:** Syntax and `Content-Type` are valid, but **semantic business validation** failed.
  * **`429 Too Many Requests`:** Client exceeded **Rate Limiting / Throttling** quota (returns **`Retry-After`** header).
* **`5xx` Server Errors:**
  * **`500 Internal Server Error`:** Unhandled server exception.
  * **`502 Bad Gateway`:** API Gateway / Reverse Proxy received an **invalid response** from an upstream server.
  * **`503 Service Unavailable`:** Server temporarily overloaded or under maintenance (`Retry-After`).
  * **`504 Gateway Timeout`:** API Gateway / Reverse Proxy did **not receive a timely response** from the upstream server before timeout expired.

### 2. SOAP (Simple Object Access Protocol) Deep Dive
* **XML Message Structure (4 Elements inside a SOAP Message):**
  1. **`<soap:Envelope>` (Mandatory Root Element):** Identifies the XML document as a SOAP message via namespace `http://www.w3.org/2003/05/soap-envelope` (SOAP 1.2) or `http://schemas.xmlsoap.org/soap/envelope/` (SOAP 1.1).
  2. **`<soap:Header>` (Optional):** First child of `Envelope` (if present); carries application-agnostic metadata such as **authentication credentials (`WS-Security`), transaction IDs, and routing rules** (`mustUnderstand="1"` attribute forces receiver to process header or fault).
  3. **`<soap:Body>` (Mandatory):** Contains the actual RPC method call name, input parameters, or response payload.
  4. **`<soap:Fault>` (Optional — inside `<soap:Body>`):** Standardized error reporting element containing `faultcode` (`VersionMismatch`, `MustUnderstand`, `Client`/`Sender`, `Server`/`Receiver`), `faultstring`, `faultactor`, and `detail`.
* **Supporting SOAP Standards (The Triad):**
  * **WSDL (Web Services Description Language):** XML contract describing **WHAT** the web service does (`<types>`, `<message>`, `<portType>` / `<interface>`), **HOW** it is accessed (`<binding>`), and **WHERE** it is located (`<service>` / `<port>` endpoint URL).
  * **UDDI (Universal Description, Discovery, and Integration):** XML-based registry/directory for publishing and discovering web services.
  * **WS-Security (Web Services Security):** OASIS extension adding message-level integrity (**XML Digital Signature**), confidentiality (**XML Encryption**), and security token propagation (UsernameToken, X.509, SAML, Kerberos) directly inside `<soap:Header>`.

### 3. GraphQL Deep Dive
* **Core Operations (3 Types in GraphQL SDL):**
  1. **`Query`:** Read-only data fetch (analogous to `GET`; sub-fields execute in parallel).
  2. **`Mutation`:** Modifies server-side data — Create, Update, Delete (analogous to `POST`/`PUT`/`DELETE`; top-level mutation fields execute **sequentially** to prevent race conditions).
  3. **`Subscription`:** Long-lived real-time event stream (typically over **WebSockets**) pushing updates to the client whenever server events occur.
* **Key GraphQL Concepts for MCQs:**
  * **Schema & SDL (Schema Definition Language):** Strongly typed contract defining **Object Types**, **Scalar Types** (`Int`, `Float`, `String`, `Boolean`, `ID`), **Enums**, **Interfaces**, **Unions**, **Input Types**, and Non-Null modifiers (**`!`**).
  * **Resolvers:** Functions on the server with signature **`(parent, args, context, info)`** that populate data for each field in the schema from databases, REST APIs, or microservices.
  * **Solves Over-fetching & Under-fetching:**
    * **Over-fetching:** REST returns 30 fields of `/users/1` when mobile UI only needs `name`. GraphQL returns *only* `name`.
    * **Under-fetching ($N+1$ Requests):** REST requires calling `/users/1` then `/users/1/posts` then `/posts/{id}/comments`. GraphQL fetches the entire nested hierarchy in **one single round-trip**.
  * **The GraphQL Server-Side $N+1$ Resolver Problem & Solution (`DataLoader`):** Naive nested resolvers execute 1 query for a list of $N$ items + $N$ individual DB queries for each item's child relation. Solved using **`DataLoader`** (**Batching** + **Request-Scoped Caching/Memoization**), which coalesces $N$ individual lookups into a single `WHERE id IN (...)` batch query.
  * **Introspection (`__schema`, `__type`):** Built-in query mechanism allowing clients/tools (GraphiQL, Apollo Sandbox) to query the GraphQL server for its own schema documentation (often **disabled in production** for security hardening).

## 3.2 API Framework Development (Syllabus Section 3(i))

### Popular API Frameworks Across Languages
| Language | Framework | Key Technical Characteristics & Exam Facts |
| :--- | :--- | :--- |
| **Python** | **FastAPI** | Built on **Starlette** (ASGI async) + **Pydantic** (type-hint data validation/serialization); automatically generates **OpenAPI 3.x (Swagger UI & ReDoc)** schemas; native `async`/`await`. |
| **Python** | **Django REST Framework (DRF)** | Batteries-included toolkit on Django; uses **Serializers** (`ModelSerializer`), **ViewSets**, **Routers**, browsable API, and built-in authentication/permission classes. |
| **Python** | **Flask** | Lightweight WSGI microframework (**Werkzeug** + **Jinja2**); extensible via `Flask-RESTful` / `Flask-Smorest` and Blueprints. |
| **Java** | **Spring Boot (Spring Web / WebFlux)** | Annotations: `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PathVariable`, `@RequestParam`, `@RequestBody`; **Spring WebFlux** provides non-blocking reactive streams (**Project Reactor** `Mono`/`Flux`). |
| **JavaScript / Node.js** | **Express.js / NestJS / Fastify** | Event-driven non-blocking I/O; middleware pipeline `app.use((req, res, next) => ...)`; **NestJS** brings TypeScript decorators & Angular-style Dependency Injection. |

### Core API Framework Components
* **Routing & Parameter Binding:**
  * **Path Parameters:** Identify a specific resource (`/orders/{orderId}`).
  * **Query Parameters:** Filter, sort, or paginate a collection (`/orders?status=shipped&limit=20`).
  * **Header Parameters:** Metadata, auth tokens, content negotiation (`Authorization`, `Accept`, `X-Request-ID`).
  * **Request Body:** Payload for `POST`/`PUT`/`PATCH` (`application/json`, `multipart/form-data`).
* **Serialization & Deserialization (Marshalling):** Converting in-memory objects/ORM models to JSON/XML/Protobuf wire format and validating incoming payloads against strict schemas.
* **Middleware / Interceptors / Filters:** Pipeline functions executing **before** and **after** the route handler for cross-cutting concerns: **Request Logging, Authentication, CORS headers, Compression (`gzip`/`br`), Rate Limiting, and Global Exception Handling**.
* **API Pagination Strategies:**
  1. **Offset-Based (`?limit=20&offset=100`):** Easy to jump to page $k$, but suffers from **slow `OFFSET` scans on large SQL tables** and **duplicate/skipped rows** if new items are inserted mid-scroll.
  2. **Cursor / Keyset-Based (`?limit=20&after_cursor=eyJpZCI6MTAwfQ==`):** Uses an indexed unique column (`WHERE id > last_seen_id LIMIT 20`); **$O(1)$ index seek performance** and immune to insertion drift during infinite scrolling.
* **API Versioning Strategies:**
  1. **URI Path Versioning:** `/api/v1/users` (Most explicit and widely adopted).
  2. **Query Parameter Versioning:** `/api/users?version=1`.
  3. **Custom Header Versioning:** `X-API-Version: 1`.
  4. **Media Type / Content Negotiation Versioning:** `Accept: application/vnd.company.v1+json`.
* **Rate Limiting Algorithms:**
  * **Token Bucket:** Bucket holds up to $B$ tokens refilled at rate $R$; allows **short bursts** up to bucket capacity.
  * **Leaky Bucket:** Requests enter a FIFO queue and are processed at a **strictly constant outflow rate** (smooths out bursts).
  * **Fixed Window Counter:** Counts requests per fixed time slot (e.g., 100/minute); vulnerable to **2x burst traffic at window boundaries** (e.g., 100 requests at `00:59` + 100 at `01:01`).
  * **Sliding Window Log / Counter:** Eliminates the boundary burst spike by weighting the previous window's count with the current window's elapsed percentage.

## 3.3 Modular Development & Interface Abstraction (Syllabus Section 3(ii))
* **Core Principle — Encapsulation & Information Hiding:** APIs establish a strict **Contract (Interface)** separating the **API Provider** (internal implementation, database schema, programming language) from the **API Consumer**. Teams work in parallel without knowing each other's internal implementation.
* **Contract-First (Design-First) vs. Code-First API Development:**
  * **Contract-First (API-First):** Teams collaboratively write the formal API specification (**OpenAPI / Swagger YAML**, **AsyncAPI**, **Protobuf `.proto`**, or **GraphQL Schema**) **before** writing any backend or frontend code. Enables:
    * **Mock Servers** (frontend/mobile teams integrate immediately against mock responses).
    * **Automated Client SDK & Server Stub Generation** (`openapi-generator`).
    * **Consumer-Driven Contract Testing (e.g., Pact):** Verifies that provider changes never break expectations of existing consumers.
  * **Code-First:** Backend developers write controller classes first and auto-generate documentation from code annotations (risks coupling API design to internal database structures and delaying frontend teams).
* **Idempotency Keys (`Idempotency-Key: <UUID>`):** Used on non-idempotent `POST` endpoints (like **payment processing** `/charges`) so that if a mobile client retries due to a network timeout, the server recognizes the UUID and returns the cached result without charging the customer twice.

## 3.4 Microservices Architecture & Cloud-Native Applications (Syllabus Section 3(iii))

### Monolithic vs. Microservices Architecture
| Dimension | **Monolithic Architecture** | **Microservices Architecture** |
| :--- | :--- | :--- |
| **Deployment Unit** | Single unified deployable artifact (`.war`, `.jar`, binary) | Suite of small, **independently deployable** services modeled around **Business Domains (DDD Bounded Contexts)** |
| **Data Management** | **Shared Single Database** across all modules | **Database-per-Service** (private schema/DB per service; no direct cross-service SQL joins) |
| **Technology Stack** | Homogeneous (single language/framework) | **Polyglot Persistence & Programming** (each service chooses its best language/DB) |
| **Scaling** | Scales the entire monolith vertically or by cloning the whole app | **Fine-grained Horizontal Scaling** of only the bottleneck service |
| **Fault Isolation** | A memory leak or crash in one module can bring down the entire system | **Bulkhead & Circuit Breaker** patterns isolate failures to a single service |

### Essential Microservices Design Patterns (High-Yield Exam Concepts)
1. **API Gateway Pattern:** Single entry point for all external clients (**North-South traffic**). Handles **Routing, Authentication/JWT validation, SSL/TLS Termination, Rate Limiting, Request Aggregation, and Protocol Translation** (e.g., REST $\rightarrow$ gRPC).
2. **BFF (Backend for Frontend) Pattern:** Instead of one monolithic general-purpose API Gateway, creates tailored gateway aggregation layers for specific client types (e.g., one BFF for **Mobile**, one BFF for **Web Desktop**) to optimize payload size and call orchestration.
3. **Service Discovery (Client-Side vs. Server-Side):** Dynamically tracks changing IP addresses and ports of ephemeral container instances using a **Service Registry** (e.g., **Consul, Eureka, etcd, Kubernetes CoreDNS**).
4. **Circuit Breaker Pattern (Resilience):** Prevents cascading failures when a downstream service is unresponsive. Operates as a state machine with **3 States**:
   * **Closed (Normal):** Requests flow normally; tracks failure count/rate.
   * **Open (Tripped):** Once failures exceed threshold, fails fast **immediately** (returns fallback/cached response) without calling the struggling downstream service.
   * **Half-Open (Probe/Recovery):** After a cooldown timer expires, allows a limited number of **test requests** through; if they succeed, resets to **Closed**; if they fail, trips back to **Open**.
5. **Saga Pattern (Distributed Transactions across Microservices):** Since traditional two-phase commit (**2PC**) blocks availability across microservices, a **Saga** executes a sequence of local transactions across services. If any step fails, the Saga executes **Compensating Transactions** in reverse order to semantically undo preceding changes.
   * **Choreography-based Saga:** Decentralized; services emit and listen to domain **Events** via a message broker (Kafka/RabbitMQ) with no central coordinator.
   * **Orchestration-based Saga:** Centralized; an **Orchestrator** (e.g., AWS Step Functions, Camunda, Temporal) explicitly commands each participant service to execute or compensate.
6. **CQRS (Command Query Responsibility Segregation):** Separates the **Write Model (Commands — state mutations)** from the **Read Model (Queries — optimized denormalized projections)** for independent scaling and performance. Often paired with **Event Sourcing** (storing state as an immutable sequence of domain events rather than overwriting current rows).
7. **Strangler Fig Pattern:** Incrementally migrating a legacy monolith to microservices by placing an API Gateway/Proxy in front and routing migrated features to new microservices while legacy routes remain on the monolith until it is completely retired.
8. **Sidecar & Service Mesh Pattern (Istio, Linkerd, Envoy):** Deploys a lightweight proxy container (**Sidecar** — **Data Plane**) alongside each microservice container in the same Kubernetes Pod to handle **East-West** service-to-service **mTLS encryption, traffic splitting (canary/blue-green), retries, and distributed tracing** managed by a central **Control Plane**.

### Cloud-Native Foundations & The 12-Factor App Methodology
* **CNCF Cloud-Native Pillars:** **Containers (Docker / containerd)** + **Orchestration (Kubernetes)** + **Microservices** + **Declarative APIs & Immutable Infrastructure (IaC)** + **CI/CD**.
* **Observability (The 3 Pillars):**
  1. **Logs:** Timestamped discrete event records (**ELK Stack / OpenSearch**, Fluentd, Loki).
  2. **Metrics:** Numeric time-series aggregations — CPU, memory, **RED Metrics** (**R**ate, **E**rrors, **D**uration) or **Four Golden Signals** (Latency, Traffic, Errors, Saturation) (**Prometheus & Grafana**).
  3. **Distributed Traces:** End-to-end request lifecycle tracking across microservices using **Trace ID** and **Span ID** (**OpenTelemetry, Jaeger, Zipkin**).
* **The 12-Factor App (Heroku — High-Yield Cloud-Native Principles):**
  1. **Codebase** (One codebase tracked in Git, many deploys)
  2. **Dependencies** (Explicitly declare and isolate dependencies)
  3. **Config** (Store config in **Environment Variables**, never hardcoded in code)
  4. **Backing Services** (Treat DBs/caches/queues as attached resources)
  5. **Build, Release, Run** (Strictly separate build, release, and run stages)
  6. **Processes** (Execute app as one or more **stateless** processes)
  7. **Port Binding** (Export services via self-contained port binding)
  8. **Concurrency** (Scale out via the process model)
  9. **Disposability** (Maximize robustness with **fast startup and graceful shutdown** on `SIGTERM`)
  10. **Dev/Prod Parity** (Keep development, staging, and production as similar as possible)
  11. **Logs** (Treat logs as **event streams** written to `stdout`/`stderr`)
  12. **Admin Processes** (Run admin/management tasks as one-off processes)

## 3.5 Cross-Platform Integration: Mobile, Web & Desktop (Syllabus Section 3(iv))

### Cross-Platform Security, Authentication & Browser Policies
* **CORS (Cross-Origin Resource Sharing):**
  * Browser security mechanism relaxing the **Same-Origin Policy (SOP)** (an Origin is defined by the tuple **`Scheme/Protocol + Host/Domain + Port`**, e.g., `https://example.com:443`).
  * **Simple Requests** (`GET`, `HEAD`, `POST` with standard form/text headers) are sent directly with an `Origin` header, validated against the server's `Access-Control-Allow-Origin` response header.
  * **Preflighted Requests** (requests using `PUT`, `DELETE`, `PATCH`, `Content-Type: application/json`, or custom headers like `Authorization`): Browser automatically sends an **`OPTIONS`** preflight request first checking `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, and `Access-Control-Allow-Headers`.
* **OAuth 2.0 & OpenID Connect (OIDC):**
  * **OAuth 2.0 (RFC 6749):** Framework for **Delegated Authorization** (grants an application scoped access via **Access Tokens**, **NOT** authentication).
    * **4 Roles:** **Resource Owner** (User), **Client** (Mobile/Web/Desktop App), **Authorization Server** (Issues tokens), **Resource Server** (API hosting protected data).
    * **Grant Types (Flows):**
      1. **Authorization Code Flow + PKCE (Proof Key for Code Exchange):** Industry standard for **Mobile Apps, Desktop Apps, Single-Page Web Apps (SPAs), and Server-Side Web Apps**. PKCE uses a dynamically generated `code_verifier` and hashed `code_challenge` to prevent authorization code interception attacks on public clients. *(Note: Legacy Implicit Flow is deprecated).*
      2. **Client Credentials Flow:** Used for **Machine-to-Machine (M2M) / Microservice-to-Microservice** background authentication where no end-user is present.
      3. **Refresh Token:** Long-lived token used to obtain a new short-lived **Access Token** without prompting the user to log in again.
  * **OpenID Connect (OIDC):** Identity **Authentication** layer built on top of OAuth 2.0; adds the **`id_token`** (a JWT containing user identity claims `sub`, `name`, `email`) and `/userinfo` endpoint.
* **JWT (JSON Web Token — RFC 7519):**
  * Compact, URL-safe, stateless token consisting of **3 Base64Url-encoded parts separated by dots (`Header.Payload.Signature`)**:
    1. **Header:** Token type (`"typ": "JWT"`) and signing algorithm (`"alg": "HS256"` HMAC symmetric or `"RS256"`/`"ES256"` asymmetric public/private key).
    2. **Payload (Claims):** Standard registered claims: **`iss`** (Issuer), **`sub`** (Subject), **`aud`** (Audience), **`exp`** (Expiration time), **`nbf`** (Not Before), **`iat`** (Issued At), **`jti`** (JWT ID).
       * *(Critical Security Fact: A standard JWT (`JWS`) is **Signed, NOT Encrypted**! Anyone can Base64Url-decode the payload and read its contents; never put plaintext passwords or secrets inside a standard JWT payload! Use **JWE — JSON Web Encryption** if payload confidentiality is required).*
    3. **Signature:** Cryptographic signature verifying the token was not tampered with (`401` if invalid).

### Platform-Specific API Integration Nuances (Mobile vs. Web vs. Desktop)
| Platform | Specific Integration Challenges & Solutions |
| :--- | :--- |
| **Mobile (iOS / Android / Flutter / React Native)** | Variable cellular connectivity & battery constraints $\rightarrow$ Use **GraphQL** or **Mobile BFF** to minimize round-trips, **Protobuf/gzip compression**, **Offline-First local caching** (SQLite/Room/CoreData) with delta sync (`updated_since`), **Push Notifications** (APNs / FCM) instead of constant polling, and **SSL/TLS Certificate Pinning** to prevent MITM inspection. |
| **Web (Browser SPAs / PWAs)** | Enforces **CORS** and **Same-Origin Policy**; vulnerable to **XSS** (if tokens stored in `localStorage`) and **CSRF** (if using cookies). Best practice: Store tokens in **`HttpOnly; Secure; SameSite=Strict/Lax`** cookies + CSRF protection or BFF token handler; use **WebSockets / Server-Sent Events (SSE)** for real-time updates. |
| **Desktop (Windows / macOS / Linux / Electron)** | Runs as a **Public Native Client** (cannot safely embed a static `client_secret` in distributed binaries because binaries can be decompiled); must use **OAuth 2.0 Authorization Code + PKCE** with system browser loopback/custom URI scheme redirect and store tokens in OS-level secure vaults (Windows Credential Locker, macOS Keychain). |

## 3.6 Rapid-Fire MCQ / MSQ / Fill-in-the-Blank Pointers (Module 3)
1. **Fill in the blank:** Among the 6 architectural constraints of REST defined by Roy Fielding, **`_____`** is the only optional constraint. $\rightarrow$ **Code-on-Demand**.
2. **Fill in the blank:** In HTTP, **`_____`** replaces an entire resource and is idempotent, whereas **`_____`** applies a partial modification to a resource and is not guaranteed to be idempotent. $\rightarrow$ **`PUT`**, **`PATCH`**.
3. **Fill in the blank:** HTTP status code **`_____`** means the client is unauthenticated, whereas **`_____`** means the client is authenticated but lacks permission to access the resource. $\rightarrow$ **`401 Unauthorized`**, **`403 Forbidden`**.
4. **Fill in the blank:** A SOAP message consists of a mandatory root **`_____`** element, an optional **`_____`** element, a mandatory **`_____`** element, and an optional **`_____`** element inside the body for errors. $\rightarrow$ **`Envelope`**, **`Header`**, **`Body`**, **`Fault`**.
5. **Fill in the blank:** In GraphQL, **`_____`** operations fetch data, **`_____`** operations modify data, and **`_____`** operations push real-time events to clients. $\rightarrow$ **`Query`**, **`Mutation`**, **`Subscription`**.
6. **Fill in the blank:** In microservices, the **`_____`** pattern prevents cascading failures using three states: Closed, Open, and Half-Open. $\rightarrow$ **Circuit Breaker**.
7. **Fill in the blank:** Distributed transactions across microservices with database-per-service are managed using the **`_____`** pattern via either Choreography or Orchestration with compensating transactions. $\rightarrow$ **Saga**.
8. **Fill in the blank:** A JSON Web Token (JWT) consists of three dot-separated Base64Url parts: **`_____`**, **`_____`**, and **`_____`**. $\rightarrow$ **Header**, **Payload**, **Signature**.
9. **Fill in the blank:** Browsers send an HTTP **`_____`** preflight request before executing cross-origin requests that use non-simple methods (like `DELETE` or `PUT`) or custom headers. $\rightarrow$ **`OPTIONS`**.
