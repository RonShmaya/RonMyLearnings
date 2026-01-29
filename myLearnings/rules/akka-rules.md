# Akka - Essential Development Rules

## Top Critical Points - Must Know ⚠️

1. **All messages MUST be immutable** - mutable messages = race conditions
2. **Never block in actor receive** - use `Future { blockingCall() }` instead
3. **Capture sender() before async operations** - `sender()` changes in Futures
4. **Prefer tell (!) over ask (?)** - better performance, no temporary actors
5. **Never use Await.result or Future.get** - defeats actor purpose
6. **Create actors with actorOf, never `new`**
7. **Always define explicit supervision strategy** - don't rely on defaults
8. **Keep messages small** - large messages slow serialization
9. **Use dedicated dispatcher for blocking operations** - never block default dispatcher
10. **Never share actor state between actors**

---

## Actor Design - CRITICAL ⚠️

### Single Responsibility Principle
- Each actor should handle the smallest task possible
- One responsibility per actor
- Decompose complex logic into actor hierarchies

### Immutability
- **All messages MUST be immutable** (actors run concurrently in separate threads)
- Actor state should be immutable or carefully managed
- Use `case class` for messages (automatically immutable in Scala)
- Mutable messages = unexpected behavior and race conditions

### Non-Blocking Operations
```scala
// BAD - blocks the actor
case evt => blockingCall()

// GOOD - blocking in Future
case evt => Future { blockingCall() }

// BEST - use pipeTo pattern
case evt =>
  Future { blockingCall() }.pipeTo(self)
```
- Never block inside an actor's receive
- Use `Future` for IO, database calls, HTTP requests
- Use `pipeTo` to send Future results back to actor
- Blocking causes context switches and kills performance

## Message Patterns

### Tell vs Ask
- **Prefer `!` (tell)** over `?` (ask) for better performance
- Use `ask` only when you truly need a response
- `ask` creates temporary actor and adds overhead
- Never use `Await.result` or `Future.get` - defeats purpose of actors

### Closing Over Sender
```scala
// BAD - sender() can change
case msg =>
  Future {
    someService.call()
  }.map { result =>
    sender() ! result  // WRONG sender
  }

// GOOD - capture sender reference
case msg =>
  val replyTo = sender()
  Future {
    someService.call()
  }.map { result =>
    replyTo ! result  // Correct sender
  }
```

## Serialization & Messages

### Message Size
- Keep messages small - large messages slow serialization/deserialization
- For large data, use references (IDs) instead of entire objects
- Messages between nodes must be serializable
- Consider using Protobuf, Avro for efficient serialization

### Serialization Rules
- All messages crossing JVM boundaries must be serializable
- Configure custom serializers for performance-critical messages
- Default Java serialization is slow - avoid in production
- Test serialization in unit tests

## Supervision Strategy - CRITICAL ⚠️

### Hierarchy Design
- Design proper supervisor hierarchies
- Parent actors supervise children
- Separate concerns: business logic vs supervision logic
- **Never let root guardian crash**

### Strategy Types
```scala
// Restart on specific exceptions
override val supervisorStrategy = OneForOneStrategy() {
  case _: IOException => Restart
  case _: IllegalStateException => Stop
  case _: Exception => Escalate
}
```
- `Restart`: recreate actor (most common)
- `Resume`: keep actor state, ignore failure
- `Stop`: terminate actor
- `Escalate`: let parent decide
- **Always define explicit strategy** - don't rely on defaults

## State Management

### Actor State Rules
- State is private to the actor
- **Never share actor state** between actors
- State updates only in `receive` (single-threaded guarantee)
- Use immutable collections (`Vector`, `List`, `Map`) over mutable ones
- If using mutable state, never expose it outside actor

### Avoiding Shared State
```scala
// BAD - shared mutable state
object SharedState {
  var counter = 0  // DANGEROUS
}

// GOOD - state in messages
case class UpdateCounter(value: Int)
case class GetCounter()
```

## Performance

### Router Patterns
- Use routers for parallel processing: `RoundRobinPool`, `SmallestMailboxPool`, `ConsistentHashingPool`
- Scale actors horizontally with routers
- Configure pool size based on workload (not CPU cores)

### Dispatcher Configuration
- Use dedicated dispatchers for blocking operations
- Default dispatcher for non-blocking work
- Configure thread pool sizes appropriately
- Never block on default dispatcher

### Backpressure
- Monitor mailbox size
- Implement backpressure for high-throughput scenarios
- Use Akka Streams for stream processing (built-in backpressure)

## Common Pitfalls

### Actor Creation
- **Never create actors with `new`** - always use `context.actorOf` or `system.actorOf`
- Don't create actors in constructor - do it in `preStart` if needed
- Avoid creating too many actors (millions of actors = memory overhead)

### ActorRef Rules
- **Never pass `this` or `self` to mutable structures**
- ActorRef is serializable, Actor is not
- Always use ActorRef for communication
- Don't expose actor instance directly

### Stashing
- Use `Stash` trait carefully - can cause memory issues
- Always `unstashAll()` after stashing
- Bounded stash to prevent memory leaks
- Stash is not a replacement for proper state machines

## Testing

### TestKit Rules
- Use `TestKit` for actor testing
- Use `TestProbe` for mock actors
- Set timeouts explicitly in tests
- Test supervision strategies separately

## Cluster & Distribution

### Cluster Sharding
- Use cluster sharding for distributed state
- Define proper shard allocation strategy
- Handle rebalancing gracefully
- Passivate idle entities to save memory

### Network Partitioning
- Design for split-brain scenarios
- Use Cluster Singleton with caution
- Implement idempotent message handling
- Consider message delivery guarantees: at-most-once (default), at-least-once (persistence)

### Akka Persistence
- Use event sourcing for critical state
- Snapshot regularly to speed up recovery
- Keep events small and serializable
- Handle schema evolution