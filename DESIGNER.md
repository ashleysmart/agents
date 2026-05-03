# Designer

Software design knowledge required of all agents operating in a design or
architecture capacity.

---

## Data Flow Design vs Class-Based Design

These are two fundamentally different ways to organise a system. Neither is
the default. The wrong choice for the problem produces unnecessary complexity
and poor performance.

---

### Class-Based Design

Organises the system around objects that encapsulate state and behaviour.
Objects hold their own data and expose operations on it. The design reflects
the value semantics of the domain — what things *are* and what they *do*.

**Use when**:
- The system models a domain with distinct entities, identity, and
  relationships (users, orders, accounts, rules).
- Behaviour varies by type — polymorphism and strategy patterns are natural.
- State is long-lived, mutable, and owned by individual entities.
- The number of distinct object types is large relative to the volume of
  data items.
- Correctness and expressiveness matter more than raw throughput.

**Characteristics**:
- Objects are the unit of encapsulation and change.
- Data and the logic that operates on it live together.
- Interactions are modelled as method calls between objects.
- Memory layout is determined by object structure, not by access pattern.

**Examples**: business logic engines, rule systems, domain models, APIs,
game entity/behaviour systems, configuration systems.

---

### Data Flow Design

Organises the system around the movement and transformation of data through
a pipeline or graph of operations. Data is separated from the operations
that process it. The design reflects *how data moves and is transformed*,
not what objects own it.

**Use when**:
- The system processes a large volume of uniform, repeating data items.
- Throughput and cache efficiency are primary constraints.
- The same transformation is applied to many items of the same shape.
- Processing maps naturally to a pipeline: ingest → transform → output.
- The target is a GPU, vectorised CPU, database engine, or stream processor
  where data layout in memory directly determines performance.

**Characteristics**:
- Data is stored in flat, contiguous structures (arrays, columns, buffers)
  — not scattered across heap-allocated objects.
- Operations are functions applied across a collection, not methods on
  individual objects.
- The pipeline is explicit — each stage has defined inputs and outputs.
- State is minimised between stages; stages are ideally pure functions.
- Memory access patterns are predictable and cache-friendly.

**Examples**: database query engines, GPU shaders, signal processing,
image pipelines, physics simulations, columnar analytics, stream
processors, neural network inference.

---

### Choosing Between Them

Do not default. Ask:

| Question | Points toward |
|----------|--------------|
| Am I modelling entities with identity and varied behaviour? | Class-based |
| Am I processing many uniform items at high throughput? | Data flow |
| Does correctness depend on encapsulating state per entity? | Class-based |
| Is cache efficiency or vectorisation a hard requirement? | Data flow |
| Does the domain have rich value semantics (rules, roles, types)? | Class-based |
| Is the problem a pipeline: ingest → transform → emit? | Data flow |
| Is the target a GPU, columnar store, or stream processor? | Data flow |
| Do objects interact with each other in complex, domain-specific ways? | Class-based |

**Mixed systems**: many real systems use both. A database engine uses data
flow internally (columnar storage, vectorised execution) but exposes a
class-based API to application code. A game may use class-based design for
game logic and data flow (ECS) for the physics and rendering hot path.
The boundary between the two should be explicit and intentional — data
crosses it via a defined interface, not by leaking either model into the
other.

**The wrong default**:
- Applying class-based design to a high-throughput data pipeline produces
  heap fragmentation, cache misses, and poor vectorisation — the object
  model gets in the way of the hardware.
- Applying data flow design to a rich domain model produces anemic data
  structures with logic scattered across pipeline stages — the domain
  becomes impossible to reason about.

---

See [DESIGN_REVIEWER.md](DESIGN_REVIEWER.md) for the per-principle design checklist.

---

## DRY — Don't Repeat Yourself

Every piece of knowledge must have a single, unambiguous representation in
the system. Duplication is not just copied code — it is copied logic,
copied configuration, copied documentation. When the knowledge changes,
every copy must change; miss one and the system is inconsistent.

**Applies to**: code, data schemas, configuration, documentation, test
fixtures, and build scripts.

**Violation signals**: copy-paste with minor edits, parallel if-chains that
mirror each other, the same constant defined in two places, tests that
re-implement the logic they are testing.

**Fix**: extract to a single authoritative location and reference it
everywhere. If two things look the same but change for different reasons,
they are not duplicates — do not merge them (that would violate SRP).

---

## KISS — Keep It Simple

A design should be as simple as possible and no simpler. Complexity that
does not directly serve a stated requirement is a liability: it is harder
to understand, harder to test, harder to change, and harder to debug.

**Rules**:
- Solve the problem in front of you, not the one you imagine might arrive.
- Prefer the straightforward data structure over the clever one.
- A longer but obvious solution beats a short but cryptic one.
- Abstractions must earn their place — every layer of indirection has a
  cost. Add it only when the benefit is concrete and present, not
  speculative.
- If you cannot explain a design decision in one sentence, the design is
  probably too complex.

---

## YAGNI — You Aren't Gonna Need It

Do not build something until it is required by a current, concrete
requirement. Speculative features and abstractions add complexity today for
a benefit that may never arrive.

**Rules**:
- If no acceptance criterion requires it, do not build it.
- Do not add extension points, plugin hooks, or configurability unless a
  real consumer exists right now.
- Do not generalise from one use case. Wait for the second real use case
  before extracting an abstraction — the right shape rarely reveals itself
  from one example.
- "We might need this later" is not a requirement.

**When to delete code**: Code that no longer serves a current requirement
is a liability. Delete it. Version control is the safety net — the code is
not lost, it is just not in the way.

Specifically, delete when:
- A feature is removed and its implementation is no longer on any code
  path.
- A function or class has no callers.
- A configuration key is no longer read.
- An abstraction was built speculatively and no second use case arrived.
- Dead branches survive inside a function (`if False`, unreachable `else`).
- A migration or one-time script has run and will never run again.

Do not leave dead code with a comment explaining why it was kept. If there
is a legitimate reason to resurrect it later, the git history and the PR
description are the record — not a comment in the source.

---

## Separation of Concerns

A system is divided into distinct sections, each responsible for one
concern. A concern is any piece of information or behaviour that affects
the program — business logic, persistence, presentation, authentication,
logging, and so on.

**Rules**:
- A module that handles HTTP routing does not contain SQL. A module that
  contains SQL does not format output. Each layer knows only about the
  layer directly below it.
- Cross-cutting concerns (logging, auth, caching) are handled at the
  boundary — via middleware, decorators, or interceptors — not woven
  through business logic.
- When a change to one concern (e.g. switching databases) forces changes
  in another (e.g. formatting), the boundary is in the wrong place.

**Layers** (typical):
- **Presentation** — format and deliver output; knows nothing about storage.
- **Application / Use case** — orchestrates domain logic; knows nothing
  about HTTP or SQL.
- **Domain** — core business rules and entities; has no external
  dependencies.
- **Infrastructure** — databases, queues, external APIs; implements
  interfaces defined by the domain.

**Relation to other principles**: SoC is the motivation behind SRP, DRY,
and layered architecture. If two things change for different reasons, they
are different concerns and should be separated.

---

## Composition over Inheritance

Prefer assembling behaviour from small, focused components rather than
deriving it through class hierarchies.

**Why inheritance fails at scale**:
- A deep hierarchy couples a subclass to every ancestor's implementation.
  A change in a base class ripples unpredictably downward.
- Inherited behaviour cannot be selectively replaced — you take all of it
  or none of it.
- Multiple inheritance (or mixing in many base classes) creates ambiguity
  and fragility.
- The hierarchy encodes assumptions about relationships that often stop
  being true as requirements change.

**Composition**:
- An object holds references to collaborators and delegates to them.
- Behaviour is swapped at runtime or at construction by injecting a
  different collaborator (Strategy pattern).
- Each collaborator is independently testable and reusable.
- The object's interface stays stable even when its internals change.

**When inheritance is appropriate**:
- A genuine is-a relationship exists and the subtype truly honours the
  base contract (LSP).
- The hierarchy is shallow (one or two levels) and unlikely to deepen.
- Abstract base classes used purely to define an interface — no shared
  mutable state, minimal shared logic.

### Toolkits vs Frameworks

These represent opposite sides of the inversion of control boundary.

**Toolkit**: a library of components you call. You are in control of the
program's structure and flow. You reach into the toolkit when you need it.
Examples: a date library, an HTTP client, a JSON parser.

- You own `main`.
- You decide the architecture.
- You can adopt or drop individual pieces without restructuring.
- Testing does not require the toolkit to be running.

**Framework**: a skeleton that calls your code. You fill in the blanks the
framework defines. The framework owns the program's flow.
Examples: a web framework, an ORM, a test runner.

- The framework owns `main` (or its equivalent).
- It dictates the structure — where to put routes, handlers, models.
- Replacing it requires restructuring the application.
- Testing often requires the framework to be bootstrapped.

**Default to toolkits.** A toolkit gives you the capability without
surrendering control. Reach for a framework only when its conventions solve
a concrete, present structural problem that toolkit composition cannot
address as simply.

**Choosing**:
- Use a toolkit unless there is a specific, concrete reason not to.
- A framework is acceptable when its conventions eliminate a class of
  structural problems that would otherwise require significant bespoke
  scaffolding — and the lock-in cost is explicitly understood and accepted.
- Never adopt a framework speculatively (YAGNI). The switching cost is high
  and grows with every line of code written against its API.
- If a framework is adopted, keep domain logic free of framework types and
  base classes. Domain code must be importable and testable without
  bootstrapping the framework.
- If you find yourself fighting a framework — working around its
  conventions, mocking its internals in tests, or bending your domain to
  fit its model — replace it with toolkits.

---

## Law of Demeter

A unit should talk only to its immediate collaborators. Do not reach through
an object to call methods on the objects it holds.

**The rule**: a method may call methods on:
1. Itself.
2. Objects passed to it as arguments.
3. Objects it constructs directly.
4. Its own direct fields/properties.

It must not call methods on objects returned by any of those calls.

**Signal of violation** — chains of dots:
```
# bad — reaching through order into customer into address
city = order.get_customer().get_address().get_city()

# good — ask the order directly
city = order.get_shipping_city()
```

Each extra dot in a chain is a dependency on the internal structure of an
object you do not own. If that structure changes, your code breaks.

**Reducing cross-coupling**:
- Pass in what you need, do not navigate to it.
- Tell objects what to do; do not ask them for data and act on it yourself
  (tell, don't ask).
- A unit that requires many imports from many different modules is coupled
  to all of them — narrow the interface and inject only what is needed.
- Coupling travels in one direction: high-level policy depends on
  abstractions; low-level detail implements them. Never reverse this.
- Shared mutable state is the strongest form of coupling — eliminate it
  before all other coupling.

**Metrics to watch**:
- **Afferent coupling (Ca)** — how many modules depend on this one. High Ca
  means a change here has wide impact; stabilise the interface.
- **Efferent coupling (Ce)** — how many modules this one depends on. High Ce
  means this module is fragile to changes elsewhere; reduce imports.
- **Instability (I = Ce / (Ca + Ce))** — a module with high instability
  (close to 1) changes often and has few dependents. A module with low
  instability (close to 0) is stable and depended upon by many — its
  interface must be locked down.

---

## Principle of Least Astonishment

Code should behave exactly as a knowledgeable reader would expect. If a
reader has to stop and ask "why does it do that?", the design has failed.

**Rules**:
- Functions do what their name says, nothing more.
- A function named `get_` or `find_` returns data — it does not mutate state.
- A function named `save_`, `update_`, or `delete_` mutates state — it does
  not silently return computed data as a side effect.
- Errors are surfaced, not swallowed. Silent failures are maximally
  astonishing.
- Boolean parameters are avoided — `process(True)` tells the reader nothing.
  Use named arguments or separate functions.
- Consistent conventions are more important than local cleverness. If the
  codebase names things one way, follow it.

**Code smells** — signals that surprise is likely:

| Smell | Why it astonishes |
|-------|------------------|
| Long method | Reader cannot hold the whole thing in mind |
| Deep nesting | Control flow is hard to trace |
| Flag argument | A single function doing two different things |
| Inconsistent naming | Same concept named differently in different places |
| Surprise side effect | A getter that writes, a query that deletes |
| Implicit ordering | Functions that must be called in a specific order with no enforcement |
| Returned `None` as sentinel | Caller cannot distinguish "not found" from "error" |
| Boolean blindness | Raw `true`/`false` with no semantic label at the call site |

When a smell is found: rename, extract, or restructure until the code reads
as the simplest possible expression of its intent.

---

## Cohesion and Coupling

**High cohesion** — the elements inside a module belong together. They
share a purpose, operate on the same data, and change for the same reason.
A module with low cohesion is doing unrelated things and should be split.

**Low coupling** — modules depend on as little of each other as possible.
A change inside one module should not force changes in others.

These two goals reinforce each other: when a module has high cohesion, its
interface is naturally narrow, which reduces the surface other modules must
couple to.

**Signals of low cohesion**: a module name that contains "and", "or",
"util", or "manager"; methods that share no data with each other; a module
that is edited for many unrelated reasons.

**Signals of high coupling**: a change to one module requires edits in
many others; tests for module A require constructing module B and C;
circular imports.

---

## Encapsulation

Hide internal implementation. Expose only what consumers need.

**Rules**:
- Fields are private by default. Expose a field only when there is a
  concrete reason for a consumer to read or write it.
- Expose behaviour, not data. A consumer should tell an object what to do,
  not extract its data and act on it externally (tell, don't ask).
- Internal representation can change freely as long as the public interface
  stays stable.
- Avoid exposing collection internals — return an immutable view or a copy,
  not a reference to the internal collection.

**Violation signals**: public fields on domain objects; getters and setters
for every field (a data class pretending to be an object); logic that
belongs inside an object living in its caller instead.

---

## Command Query Separation (CQS)

A function either changes state or returns data — never both.

| Type | Does | Returns |
|------|------|---------|
| **Command** | Mutates state | Nothing (void / unit) |
| **Query** | Reads state | A value |

**Why**: a query can be called freely — it is safe to call multiple times,
in any order, without side effects. A command changes the world. Mixing the
two means a caller cannot read without risk and cannot trust that reading
is free.

**Exception**: factory methods that construct and return a new object are
acceptable. The exception is narrow and intentional — document it.

---

## Fail-Fast

Detect and surface problems at the earliest possible point. Do not allow
invalid state to propagate deeper into the system where it is harder to
trace and more expensive to fix.

**Rules**:
- Validate inputs at the boundary — the moment data enters the system.
- Raise an error immediately on invalid state; do not carry it forward with
  a sentinel value.
- Do not write defensive checks deep inside business logic for conditions
  that should have been caught at the entry point.
- An assertion that fires early is better than a corrupted database record
  discovered later.

---

## Defensive Programming

Assume misuse and guard against it at system boundaries.

**Where to apply it**:
- Public APIs — validate every input before acting on it.
- User input — treat as untrusted; validate type, range, and format.
- File and network IO — handle missing files, timeouts, and malformed data
  explicitly.
- Inter-service calls — assume the remote can fail, return unexpected data,
  or be slow.

**Where not to apply it**:
- Inside a module's own private functions — once data has passed the
  boundary and been validated, internal functions can trust it.
  Defensive checks on every internal call add noise without safety.

Fail-fast and defensive programming work together: defensive programming
defines the boundary; fail-fast ensures violations are surfaced immediately
rather than silently tolerated.

---

## Domain-Driven Design (DDD)

Organise software around the business domain. The model in code should
reflect the model the domain experts use to reason about the problem.

**Core concepts**:

| Concept | Meaning |
|---------|---------|
| **Entity** | An object with a distinct identity that persists over time (e.g. a `User` with an `id`) |
| **Value Object** | An object defined entirely by its attributes; no identity; immutable (e.g. a `Money` amount) |
| **Aggregate** | A cluster of entities and value objects treated as a single unit; one root entity controls access |
| **Aggregate Root** | The entry point to an aggregate; external code only holds references to the root |
| **Repository** | Abstracts persistence for an aggregate; the domain does not know about SQL or storage |
| **Domain Service** | Stateless logic that does not belong to a single entity or value object |
| **Domain Event** | A record that something meaningful happened in the domain; named in past tense |
| **Bounded Context** | An explicit boundary within which a model is defined and consistent; different contexts may use different models for the same concept |
| **Ubiquitous Language** | A shared vocabulary between engineers and domain experts; used in code, docs, and conversation |

**Rules**:
- Business rules live in the domain layer — not in controllers, not in
  repositories, not in the framework.
- Aggregates are the consistency boundary. Only the aggregate root enforces
  invariants across its members.
- Cross-aggregate references are by identity only — never hold a direct
  object reference across an aggregate boundary.
- Each bounded context has its own model. Do not share entity classes
  between contexts — map explicitly at the boundary.

---

## SOLID Principles

| Abbr | Full name | One-line rule |
|------|-----------|---------------|
| **SRP** | Single Responsibility | One reason to change per module, class, or function |
| **OCP** | Open / Closed | Open for extension, closed for modification |
| **LSP** | Liskov Substitution | Subtypes must be drop-in replacements for their base type |
| **ISP** | Interface Segregation | Many narrow interfaces over one wide one |
| **DIP** | Dependency Inversion | Depend on abstractions, not concretions |

### SRP — Single Responsibility

A module has exactly one reason to change. When a unit does two things —
parsing *and* persisting, validating *and* formatting — split it. The name
of each unit must make its single responsibility obvious without reading
the body.

### OCP — Open / Closed

New behaviour is added by extending, not by editing existing code. Achieve
this through composition, strategy objects, and well-defined extension
points. A change request that requires editing an existing class body is a
signal the abstraction boundary is in the wrong place.

### LSP — Liskov Substitution

Every subtype must honour the full contract of its base type. A caller
holding a reference to the base type must observe identical behaviour
regardless of which concrete subtype it receives. Violations: a subtype
that throws where the parent does not, returns a narrower type, or silently
ignores a method.

### ISP — Interface Segregation

No implementor should be forced to define methods it does not use. Split
wide interfaces into focused ones. Clients depend only on the slice of the
interface they actually call.

### DIP — Dependency Inversion

High-level policy modules do not import low-level detail modules. Both
depend on a shared abstraction. Concrete implementations are injected at
the composition root — never constructed inside business logic.

---

## ACID Properties

Properties that guarantee validity of database transactions.

| Property | Guarantee |
|----------|-----------|
| **Atomicity** | A transaction either completes fully or has no effect — no partial writes |
| **Consistency** | A transaction moves the database from one valid state to another |
| **Isolation** | Concurrent transactions produce the same result as if run serially |
| **Durability** | A committed transaction survives system failure |

### Isolation Levels

Isolation levels trade consistency for concurrency. From weakest to
strongest:

| Level | Dirty Read | Non-repeatable Read | Phantom Read |
|-------|-----------|---------------------|--------------|
| Read Uncommitted | ✅ possible | ✅ possible | ✅ possible |
| Read Committed | ❌ prevented | ✅ possible | ✅ possible |
| Repeatable Read | ❌ prevented | ❌ prevented | ✅ possible |
| Serializable | ❌ prevented | ❌ prevented | ❌ prevented |

### Read / Write Phenomena

**Dirty read** — a transaction reads data written by another transaction
that has not yet committed. If the writer rolls back, the reader has seen
data that never existed.

**Non-repeatable read** — a transaction reads the same row twice and gets
different values because another transaction committed a change between the
two reads.

**Phantom read** — a transaction re-runs a query and sees a different set
of rows because another transaction inserted or deleted rows that match the
query predicate.

**Read before commit** — a pattern that requires a value to be read in the
same transaction before it can be safely overwritten, ensuring the write is
based on current state and not a stale snapshot.

**Lost update** — two transactions read the same value, both compute an
update, and the second write silently overwrites the first. Prevented by
pessimistic locking or compare-and-swap.

**Write skew** — two transactions each read an overlapping data set,
make decisions based on what they read, and write to non-overlapping rows
— producing a result that neither transaction would have allowed had it seen
the other's write.

---

## Variable Semantic Classes

Variables carry meaning beyond their type. Classify them to make intent
explicit and prevent misuse.

| Class | Meaning | Examples |
|-------|---------|---------|
| **Identity** | Uniquely names an entity; never transformed | `user_id`, `order_sid`, `node_key` |
| **State** | Current condition of an entity; mutates over time | `status`, `phase`, `is_active` |
| **Value** | A quantity or measurement; may be calculated | `count`, `total_price`, `duration_ms` |
| **Flag** | A boolean decision point; names start with `is_`, `has_`, `can_` | `is_valid`, `has_flag`, `can_retry` |
| **Accumulator** | Collects values across iterations | `results`, `errors`, `seen` |
| **Cursor / Index** | Tracks position in a sequence | `i`, `offset`, `cursor` |
| **Sentinel** | A special value signalling a boundary condition | `None`, `EOF`, `-1` as "not found" |
| **Intermediate** | A temporary result in a multi-step computation; should be short-lived | `raw`, `parsed`, `filtered` |
| **Config / Constant** | A fixed parameter; does not change at runtime | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |

Rules:
- Name the variable after its semantic class, not its type. `user_id: str`
  not `user_string: str`.
- Sentinels must be documented. A bare `None` return is ambiguous — prefer
  a typed `Optional` or result type with an explicit absent case.
- Accumulators are initialised before the loop that fills them and consumed
  after it — they should not escape further than necessary.

---

## Software Design Patterns

### Creational

| Pattern | Intent |
|---------|--------|
| **Factory Method** | Delegate instantiation to a subclass or factory function; callers depend on the interface, not the concrete type |
| **Abstract Factory** | Create families of related objects without specifying their concrete classes |
| **Builder** | Construct a complex object step-by-step; separate construction from representation |
| **Singleton** | Ensure a class has exactly one instance; prefer dependency injection over global access |
| **Prototype** | Clone an existing object rather than constructing from scratch |
| **Object Pool** | Reuse a fixed set of expensive objects rather than creating and destroying them per use |
| **Lazy Initialisation** | Delay creation of an object or computation of a value until the first time it is needed |
| **Multiton** | Like Singleton but keyed — one instance per named key, managed by the class itself |

### Structural

| Pattern | Intent |
|---------|--------|
| **Adapter** | Wrap an incompatible interface so it matches the one a client expects |
| **Bridge** | Decouple an abstraction from its implementation so both can vary independently |
| **Composite** | Treat individual objects and compositions uniformly through a shared interface |
| **Decorator** | Add behaviour to an object at runtime by wrapping it; preserves the interface |
| **Facade** | Provide a simplified interface over a complex subsystem |
| **Flyweight** | Share fine-grained objects to reduce memory when many instances share state |
| **Proxy** | Control access to an object — for lazy init, access control, or caching |
| **Module** | Group related functions, types, and state behind a single cohesive interface; hide internals |

### Behavioural

| Pattern | Intent |
|---------|--------|
| **Chain of Responsibility** | Pass a request along a chain of handlers; each handler decides to process or forward |
| **Command** | Encapsulate a request as an object; enables undo, queuing, and logging |
| **Interpreter** | Define a grammar for a language and provide an interpreter to evaluate sentences in it |
| **Iterator** | Provide sequential access to elements without exposing the underlying structure |
| **Mediator** | Centralise communication between objects to reduce direct dependencies |
| **Memento** | Capture and restore an object's state without violating encapsulation |
| **Null Object** | Provide a do-nothing implementation of an interface to eliminate null checks at call sites |
| **Observer** | Notify dependents automatically when an object's state changes |
| **Specification** | Encapsulate a business rule as a composable predicate object; combine with and/or/not |
| **State** | Allow an object to alter its behaviour when its internal state changes |
| **Strategy** | Define a family of algorithms, encapsulate each, and make them interchangeable |
| **Template Method** | Define the skeleton of an algorithm in a base class; let subclasses fill in steps |
| **Visitor** | Add operations to an object structure without modifying the classes of its elements |

### Architectural

| Pattern | Intent |
|---------|--------|
| **Repository** | Abstract persistence behind a collection-like interface; domain does not know about storage |
| **Unit of Work** | Track all changes made during a transaction and write them out as a single atomic operation |
| **Data Mapper** | Map between domain objects and database rows without coupling either side to the other |
| **Active Record** | Wrap a database row in a domain object that also handles its own load, save, and delete |
| **Service Object** | Encapsulate a single business operation in a stateless object with one public method |
| **Anti-Corruption Layer** | Translate between two different domain models at an integration boundary so neither corrupts the other |
| **Strangler Fig** | Incrementally replace a legacy system by routing requests to new implementation; retire the old gradually |
| **Hexagonal Architecture (Ports and Adapters)** | Isolate the domain from all external concerns behind ports; adapters implement the ports for specific technologies |
| **Layered Architecture** | Organise code into horizontal layers (presentation, application, domain, infrastructure) with dependencies flowing downward only |

### Distributed Patterns

| Pattern | Intent |
|---------|--------|
| **Circuit Breaker** | Stop calling a failing service after a threshold; reset after a cool-down period |
| **Retry with Backoff** | Re-attempt a failed operation with increasing delays; cap total attempts |
| **Saga** | Coordinate a long-running transaction across services using local transactions with compensating actions on failure |
| **Choreography** | Services react to events with no central coordinator; each service knows what to do when it receives an event |
| **Outbox** | Write events to a local outbox table in the same transaction as the domain write; a relay process forwards them to the broker |
| **Idempotent Consumer** | Ensure processing a message more than once has the same effect as processing it once |
| **CQRS** | Separate read and write models; reads use a denormalised projection, writes go through a command handler |
| **Event Sourcing** | Store state as an append-only sequence of events; current state is derived by replaying them |
| **API Gateway** | Single entry point for all clients that routes to backend services, handles auth, and aggregates responses |
| **Backends for Frontends (BFF)** | Separate backend API surface per client type (web, mobile, third-party) to serve each client's specific needs |
| **Sidecar** | Deploy a helper process alongside the main service to handle cross-cutting concerns (logging, proxying, config) |
| **Ambassador** | Proxy network calls from the main service through a helper that handles retries, circuit breaking, and observability |
| **Bulkhead** | Isolate components into pools so a failure in one pool does not exhaust resources for others |
| **Throttling** | Limit the rate at which a consumer can use a resource; shed excess load gracefully |
| **Competing Consumers** | Multiple consumer instances read from the same queue; each message processed by exactly one consumer |
| **Publisher-Subscriber** | Producers publish to topics; consumers subscribe independently; producers and consumers are fully decoupled |
| **Leader Election** | Designate one node as coordinator for a task; remaining nodes stand by and take over on failure |
| **Health Endpoint Monitoring** | Expose a dedicated endpoint that reports service health; consumed by load balancers and orchestrators |
| **Sharding** | Partition data across multiple storage nodes by a shard key to distribute load and storage |
| **Read Replicas** | Route read queries to replicas of the primary to offload read load and improve throughput |
| **Valet Key** | Grant a client a time-limited, scoped token to access a resource directly without routing through the service |
| **Two-Phase Commit (2PC)** | Coordinate an atomic commit across multiple participants using a prepare phase followed by a commit or abort |

---

See [reference/anti-patterns/README.md](reference/anti-patterns/README.md) for the full anti-pattern catalogue.
