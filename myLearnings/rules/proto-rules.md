# Protocol Buffers (Proto) - Essential Rules

## Top Critical Points - Must Know ⚠️

1. **Tag numbers are sacred** - never change, reuse, or modify existing field numbers
2. **Enum first value MUST be 0** - it's the default value (name it UNSPECIFIED)
3. **Never change field type** - breaks serialization completely
4. **Always reserve deleted field numbers** - prevents accidental reuse
5. **Use int64 for IDs** - int32 too small for real-world IDs
6. **Proto is API contract, not DB model** - design for 10+ years forward
7. **Proto3 can't distinguish unset from default** - use explicit optional if needed
8. **Use sint32/sint64 for negative numbers** - more efficient encoding
9. **Never expose internal models** - proto is external contract
10. **Prefer adding fields over versioning messages** - backward compatibility built-in

---

## Backward/Forward Compatibility

### NEVER ❌
- Change existing field number
- Change field type
- Reuse deleted field number
- Change semantic meaning of field

### ALWAYS SAFE ✅
- Add new field
- Delete field (but reserve its number!)
- Make field optional
- Add values to enum (at end)

## Deleting Fields Correctly
```proto
message User {
  int64 id = 1;

  reserved 2;           // Reserve number
  reserved "name";      // Reserve name

  string email = 3;
}
```

## Proto3 Defaults
- string → ""
- int → 0
- bool → false
- repeated → empty list
- No required fields
- All fields implicitly optional

## Field Types

### repeated - Arrays
- Maintains order
- 0..N items
- Never null, just empty list

### optional - Explicit Presence
- Generates hasField() method
- Use when need to distinguish unset vs default value

### oneof - Exclusive Choice
- Only one field can be set
- Setting one clears others
- Perfect for events/polymorphism

### map
- Limited to simple key types
- `map<string, int32> scores = 1;`

## Number Types
- `int32` - regular positive numbers
- `int64` - use for IDs always
- `sint32/sint64` - use when values can be negative
- `fixed32/fixed64` - when values typically large

## Enums
```proto
enum Status {
  STATUS_UNSPECIFIED = 0;  // MUST be first, always
  ACTIVE = 1;
  BLOCKED = 2;
}
```
- 0 is default value
- Never delete values
- Never change order
- Always name 0 as UNSPECIFIED/UNKNOWN

## Well-Known Types
- Use `google.protobuf.Timestamp` instead of int64
- Use `google.protobuf.Duration` instead of int64
- More semantic, better for evolution

## Design Rules

### Message Design
- Break into small messages, not flat 20-field objects
- Think: "What am I committing to for 10 years?"
- Proto is API contract, not internal model

### Avoid
- Boolean fields with double meaning → use enum instead
- Exposing DB entities directly
- "Beautiful objects" → focus on contract
- Complex nested structures → flatten when possible

## Versioning Strategy

### Adding Field - Safe
```proto
// Old
message User {
  int64 id = 1;
}

// New - old clients ignore new field
message User {
  int64 id = 1;
  string email = 2;
}
```

### Changing Type - FORBIDDEN
```proto
// Solution: deprecate + add new
string price = 1 [deprecated = true];
int64 price_cents = 2;
```

## Kafka/Event Streaming Patterns
- Store proto in shared repo
- Use schema registry
- Events are immutable
- Use oneof for event types:
```proto
message DeviceEvent {
  string device_id = 1;
  google.protobuf.Timestamp ts = 2;

  oneof event {
    Telemetry telemetry = 3;
    Alarm alarm = 4;
  }
}
```