# Encore Plugin for Cursor

Build backends in TypeScript and Go with automatic infrastructure. This plugin provides rules, skills, an MCP server, an agent, and commands for building Encore applications in Cursor.

## What's included

- **Rules**: Encore conventions for TypeScript and Go (APIs, services, infrastructure primitives, project structure)
- **Skills**: Create services, add infrastructure, debug with traces
- **Agent**: Backend development assistant with live MCP context
- **MCP**: Connects Cursor to your running Encore app for introspecting services, databases, traces, and more
- **Commands**: Run and deploy your app

## Setup

1. Install the Encore CLI: https://encore.dev/docs/install
2. Create or open an Encore project
3. Run `encore run` to start your app
4. The MCP server connects automatically when configured

Set your app ID in the MCP configuration by replacing `${ENCORE_APP_ID}` with your actual Encore app ID (find it with `encore app ls`).

## Links

- [Encore Documentation](https://encore.dev/docs)
- [Encore GitHub](https://github.com/encoredev/encore)
- [Discord Community](https://encore.dev/discord)
