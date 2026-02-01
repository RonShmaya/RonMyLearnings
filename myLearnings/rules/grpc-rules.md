# gRPC - Essential Rules

## Top Critical Points - Must Know ⚠️

1. **Always set deadlines/timeouts** - prevent hanging calls forever
2. **4 call types: Unary, Server Stream, Client Stream, Bidirectional** - know which to use
3. **Status codes matter** - use correct codes (OK, INVALID_ARGUMENT, NOT_FOUND, etc.)
4. **Idempotency for retries** - only retry safe operations
5. **Channel is expensive** - reuse channels, don't create per-call
6. **Metadata for cross-cutting concerns** - auth tokens, tracing, request IDs
7. **Stream cancellation propagates** - client cancel → server notified
8. **Use interceptors for common logic** - logging, auth, metrics
9. **Proto breaking changes break clients** - follow proto compatibility rules
10. **Unary = request-response, use for most cases** - simplest and most common

---

## Call Types

### Unary RPC
- Single request → single response
- Most common pattern
- Use for simple request/response

### Server Streaming
- Single request → multiple responses
- Server sends stream of data
- Use for: large result sets, real-time updates

### Client Streaming
- Multiple requests → single response
- Client sends stream of data
- Use for: file uploads, batch operations

### Bidirectional Streaming
- Multiple requests ↔ multiple responses
- Both sides stream independently
- Use for: chat, real-time collaboration

## Deadlines & Timeouts
- Always set deadline on client side
- Deadline propagates across service calls
- Server checks deadline automatically
- Use reasonable timeouts per operation type

## Status Codes (Use Correct Ones)
- `OK` - success
- `CANCELLED` - client cancelled
- `INVALID_ARGUMENT` - bad request data
- `NOT_FOUND` - resource doesn't exist
- `ALREADY_EXISTS` - duplicate creation
- `PERMISSION_DENIED` - auth failed
- `UNAUTHENTICATED` - missing/invalid credentials
- `RESOURCE_EXHAUSTED` - rate limit/quota
- `UNAVAILABLE` - temporary failure, retry
- `INTERNAL` - server error
- `UNIMPLEMENTED` - method not implemented
- `DEADLINE_EXCEEDED` - timeout

## Error Handling
- Return proper status codes
- Include error details in status
- Use status.WithDetails() for structured errors
- Client must handle all possible statuses
- Retry only on safe status codes (UNAVAILABLE, DEADLINE_EXCEEDED)

## Metadata
- Key-value pairs sent with RPC
- Use for: auth tokens, tracing IDs, request IDs
- Prefix custom metadata with company/app name
- Binary metadata suffix: `-bin`
- Access via context on both sides

## Channel Management
- Channel = connection to server
- Expensive to create - reuse channels
- One channel per service endpoint
- Channel handles connection pooling internally
- Close channel on shutdown

## Interceptors
- Client interceptors - outgoing calls
- Server interceptors - incoming calls
- Chain multiple interceptors
- Use for: logging, metrics, auth, retry logic
- Order matters in chain

## Retry & Hedging
- Configure retry policy in service config
- Only retry idempotent operations
- Exponential backoff for retries
- Hedging = send duplicate request after delay
- Use status codes to decide retry

## Idempotency
- Safe to retry: GET-like operations
- Unsafe to retry: mutations without idempotency key
- Use idempotency tokens for retry-safe mutations
- Server must detect and handle duplicate requests

## Load Balancing
- Client-side load balancing built-in
- Pick First (default) - single connection
- Round Robin - distribute across backends
- Configure via service config
- DNS-based discovery common

## Streaming Best Practices
- Send messages in batches when possible
- Handle backpressure (slow consumer)
- Check context.Done() for cancellation
- Always close streams properly
- Server: return error vs complete stream

## Security
- Always use TLS in production
- mTLS for service-to-service auth
- Token-based auth via metadata
- Validate credentials in interceptor

## Proto Service Definition
- Service defines RPC methods
- Keep RPCs focused and simple
- Version services explicitly
- Document expected errors
- Use streaming when appropriate, not by default

## Performance
- Reuse channels
- Use connection pooling (automatic)
- Stream for large payloads
- Compress large messages
- Monitor and set appropriate timeouts

## Common Pitfalls

### AVOID
- Creating channel per request
- No deadlines/timeouts
- Retrying non-idempotent operations
- Using wrong status codes
- Ignoring cancellation signals
- Blocking in stream handlers
- Not closing channels
- Plain text in production

### DO
- Reuse channels across requests
- Always set deadlines
- Use correct status codes
- Handle context cancellation
- Close streams and channels properly
- Use TLS in production
- Implement proper error handling
- Add interceptors for cross-cutting concerns
