---
name: debug-traces
description: Debug Encore applications using distributed traces. Use when the user reports an error, wants to understand request flow, or needs to diagnose performance issues. Requires the MCP server to be running.
---

# Debug with Encore Traces

## When to use

- User reports an error or unexpected behavior
- User wants to understand how a request flows through services
- User wants to find performance bottlenecks
- User asks "why is this slow" or "why is this failing"

## Instructions

1. Use the MCP `get_traces` tool to fetch recent traces. Filter by endpoint or status if the user mentions a specific API.

2. Find the failing or slow trace and use `get_trace_spans` to inspect the full span tree.

3. Traces show the complete request lifecycle:
   - HTTP ingress (method, path, status code, duration)
   - Auth handler execution
   - Database queries (with bound parameters and timing)
   - Service-to-service calls
   - Pub/Sub publish and subscribe operations
   - Cache hits and misses
   - Outbound HTTP requests

4. Look for:
   - Spans with error status
   - Spans with unusually high duration
   - N+1 query patterns (many sequential database queries)
   - Missing spans that indicate a service didn't respond

5. Use `query_database` to inspect data state if needed.

6. Use `get_services` and `get_metadata` to understand the architecture if the trace spans multiple services.

7. After identifying the issue, suggest a fix in the relevant source file. Use `get_src_files` to read the current implementation.

## Key rule

Always check traces before guessing at the problem. The trace data is authoritative.
