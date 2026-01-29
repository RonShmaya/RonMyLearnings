# Development Rules

Essential development rules and best practices for various technologies.

## Available Rules

### [Kafka Streams](./kafka-streams-rules.md)
Critical rules for Kafka Streams development focusing on windowing, time management, joins, and error handling.

**Top Priorities:**
- Grace period configuration (avoid 24h default)
- Suppress usage and BufferConfig
- Window closing behavior (event-driven)
- Join partitioning requirements

### [Akka](./akka-rules.md)
Critical rules for Akka actor system development focusing on message immutability, non-blocking operations, and supervision.

**Top Priorities:**
- Message immutability
- Non-blocking operations (Future patterns)
- Sender capture in async operations
- Supervision strategy definition

---

## Purpose

These rule files serve as:
- Quick reference guides during development
- Code review checklists
- Context for AI-assisted development (Claude)
- Team knowledge base

## Format

Each rule file follows this structure:
1. **Top Critical Points** - 10 most important things to know
2. **Detailed Rules** - Organized by category with code examples
3. Focus on common pitfalls and production-ready patterns
