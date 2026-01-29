# Kafka Streams - Essential Rules

## Stateless Operations
- Never configure stateful-only params for stateless operations
- Prefer `mapValues()` over `map()` to avoid repartitioning

## Stateful Operations
- Always be aware of which timestamp drives processing (aggregate, count, windowed ops, joins)
- Stores don't emit immediately by default (flush every 30s OR cache full 10MB)
- `num.standby.replicas` only applies to stateful operations

## Joins
- Both sides must have same key → same partition
- Stream-Stream: requires time window
- Stream-Table: no window needed
- Table-Table: requires materialized state store

## Windowing & Time - CRITICAL ⚠️
- **Always define grace explicitly** - keep as small as possible (default 24h is dangerous)
- **Never use Suppress without well-defined grace**
- Large grace + suppress = high OOM risk
- Suppress is stateful and heavy - always define BufferConfig
- **If you don't need final result → don't use suppress**
- Windows only close when new event arrives to partition (stream time advances)
- Formula: `streamTime > windowEnd + grace ⇒ window closed`
- Stream time = per partition, always forward
- Late events dropped only if arrived after `window end + grace`

## Error Handling
- **Entry (Deserialize)**: Default `LogAndFail` → recommended `LogAndContinue`
- **Processing (Runtime)**: Default `SHUTDOWN_APPLICATION` → consider `REPLACE_THREAD`
- **Exit (Produce)**: Default `FAIL` → consider `CONTINUE`
- Rule: Be explicit about error handling - defaults too aggressive for production

## Stream Types
- **KStream**: event stream, each record independent
- **KTable**: latest value per key, holds only partition data
- **GlobalKTable**: all partitions on every instance (use for small reference data only)

## Checklist
- [ ] Stateful ops: timestamp awareness confirmed?
- [ ] Stateful ops: grace period explicitly defined (not 24h default)?
- [ ] Suppress used: BufferConfig defined?
- [ ] Suppress needed: is final result truly required?
- [ ] Joins: same key and partition count on both sides?
- [ ] Error handlers: all three types explicitly configured?
- [ ] Windowing: grace period as small as possible?