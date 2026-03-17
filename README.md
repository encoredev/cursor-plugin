# Encore Plugin for Cursor

Build backends in TypeScript and Go with automatic infrastructure. This plugin provides rules, skills, an MCP server, an agent, and commands for building Encore applications in Cursor.

## What's included

### MCP Server

The plugin configures Cursor to connect to your Encore app via the [Model Context Protocol](https://modelcontextprotocol.io). Cursor launches the MCP server automatically through the plugin's `mcp.json` config. You just need your app running with `encore run` for the tools to return live data.

The MCP server gives the agent:

- **Inspect services & APIs**: view all services, endpoints, auth handlers, and middleware
- **Query databases**: run SQL against your app's PostgreSQL databases
- **Call endpoints**: test any API endpoint and inspect the response
- **Analyze traces**: read distributed traces to debug errors and find performance bottlenecks
- **Browse Pub/Sub**: see topics, subscriptions, and message delivery
- **Check caches**: inspect cache keyspaces and their configuration
- **View cron jobs**: see all scheduled jobs and their endpoints
- **Read source files**: access any file in the project
- **Search docs**: look up Encore documentation without leaving the editor

### Rules

Encore conventions for TypeScript and Go: API definitions, service structure, infrastructure primitives (databases, Pub/Sub, cron, caching, object storage, secrets), auth patterns, and project layout.

### Skills

- **create-service**: scaffold a new Encore service with endpoints, database, and infrastructure
- **add-infrastructure**: add databases, Pub/Sub topics, cron jobs, caches, object storage, or secrets to an existing service
- **debug-traces**: diagnose errors and performance issues using distributed traces from the MCP server

### Agent

`encore-assistant`: a backend development assistant that uses the MCP server to understand your architecture before writing code. It inspects existing services, follows your project's patterns, and verifies changes through traces.

### Commands

- **run-app**: start the app locally with `encore run`
- **deploy**: deploy to Encore Cloud or build a Docker image for self-hosting

## Setup

1. [Install the Encore CLI](https://encore.dev/docs/install)
2. Create or open an Encore project
3. Replace `${ENCORE_APP_ID}` in the MCP config with your app ID (find it with `encore app ls`)
4. Run `encore run` to start your app. The MCP server launches automatically through Cursor, but needs the app running to provide trace data, database access, and endpoint testing.

## Example prompts

Once the plugin is installed and your app is running:

- "Add a PostgreSQL database to the users service with a migrations table for profiles"
- "Create a Pub/Sub topic for order events and a subscription that sends confirmation emails"
- "My /api/checkout endpoint is returning 500s, check the traces and find out why"
- "Add a cron job that cleans up expired sessions every hour"
- "Show me all the services and how they connect to each other"
- "Query the users database and show me the schema"

## Links

- [Encore Documentation](https://encore.dev/docs)
- [Encore GitHub](https://github.com/encoredev/encore)
- [Discord Community](https://encore.dev/discord)
