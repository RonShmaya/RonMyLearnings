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