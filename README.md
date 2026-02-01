# Ron's Learning Repository

A curated collection of development rules, best practices, and technical learnings.

## 📚 Structure

```
myLearnings/
├── rules/          # Essential development rules and best practices
├── tech/           # Technology-specific learnings
└── lenguages/      # Programming language learnings
```

## 🎯 Development Rules

Quick reference guides for critical development decisions and best practices:

### [Kafka Streams Rules](myLearnings/rules/kafka-streams-rules.md)
Essential rules for Kafka Streams development including:
- Windowing & Time management (grace periods, suppress, OOM risks)
- Stateful vs Stateless operations
- Join requirements
- Error handling strategies
- Stream types (KStream, KTable, GlobalKTable)

**Critical Points:**
- Always define grace explicitly (default 24h is dangerous)
- Never use Suppress without well-defined grace
- Windows only close when new events arrive (event-driven, not timer-based)

### [Akka Rules](myLearnings/rules/akka-rules.md)
Essential rules for Akka actor system development including:
- Actor design principles (immutability, non-blocking operations)
- Message patterns (tell vs ask, sender capture)
- Supervision strategies
- State management
- Performance optimization (routers, dispatchers, backpressure)

**Critical Points:**
- All messages MUST be immutable
- Never block in actor receive - use Future
- Capture sender() before async operations
- Prefer tell (!) over ask (?)

### [Protocol Buffers Rules](myLearnings/rules/proto-rules.md)
Essential rules for Protocol Buffers schema design including:
- Backward/forward compatibility
- Field numbers and types
- Enum design (0 = default)
- Proto3 defaults and optional fields
- Versioning strategies

**Critical Points:**
- Tag numbers are sacred - never change, reuse, or modify
- Enum first value MUST be 0 (UNSPECIFIED)
- Always reserve deleted field numbers
- Use int64 for IDs, sint32 for negatives
- Proto is API contract for 10+ years, not DB model

### [Scala Rules](myLearnings/rules/scala-rules.md)
Essential rules for writing Scala code including:
- Constructor parameters (val/var/nothing)
- Immutability and recursion
- Pattern matching and for comprehensions
- Option/Try/Future for safe error handling
- Case classes and collections

**Critical Points:**
- Everything is expression - returns value, not statement
- Use recursion not loops
- val by default, avoid var
- Never null - use Option[T]
- Case class params auto-val, regular class params need explicit val

## 🔧 Technology-Specific Rules

Technical rules for specific technologies and frameworks:

### [gRPC Rules](myLearnings/tech/grpc-rules.md)
Essential rules for gRPC service development including:
- Call types (Unary, Server/Client/Bidirectional streaming)
- Deadlines and timeouts
- Status codes and error handling
- Channel management and reuse
- Interceptors and metadata

**Critical Points:**
- Always set deadlines/timeouts - prevent hanging calls
- Reuse channels - creating per-call is expensive
- Use correct status codes - UNAVAILABLE, INVALID_ARGUMENT, etc.
- Only retry idempotent operations
- Unary for most cases, streaming when appropriate

## 🚀 Usage

These rules are designed for:
- **Developers**: Quick reference during development and code reviews
- **Claude/AI**: Context for generating and reviewing code
- **Teams**: Shared understanding of best practices

## 📖 How to Read

Each rule file contains:
1. **Top Critical Points** - The most important things to know (always read first)
2. **Detailed Rules** - Organized by category with examples and explanations

Start with the "Top Critical Points" section to understand the most common pitfalls and critical decisions.