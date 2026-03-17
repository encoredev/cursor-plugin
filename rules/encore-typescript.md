---
description: "Encore.ts conventions for APIs, services, infrastructure primitives, and project structure"
---

# Encore TypeScript Conventions

## Project Structure

Encore apps are organized into services. Each service is a directory with an `encore.service.ts` file. Services can contain multiple API endpoints, databases, pub/sub topics, and other infrastructure.

```
my-app/
├── service-a/
│   ├── encore.service.ts
│   ├── api.ts
│   └── db.ts
├── service-b/
│   ├── encore.service.ts
│   └── api.ts
└── package.json
```

## Defining Services

Every service needs an `encore.service.ts` file:

```typescript
import { Service } from "encore.dev/service";
export default new Service("my-service");
```

## API Endpoints

Define endpoints using the `api` function from `encore.dev/api`:

```typescript
import { api } from "encore.dev/api";

interface CreateRequest {
  name: string;
}

interface CreateResponse {
  id: string;
  name: string;
}

export const create = api(
  { expose: true, method: "POST", path: "/items" },
  async (req: CreateRequest): Promise<CreateResponse> => {
    // implementation
  }
);
```

Options: `expose` (public), `auth` (requires authentication), `method`, `path`.

For raw HTTP access (webhooks, file uploads):

```typescript
export const webhook = api.raw(
  { expose: true, method: "POST", path: "/webhook" },
  async (req, res) => {
    // req is http.IncomingMessage, res is http.ServerResponse
  }
);
```

## Databases

Declare a PostgreSQL database:

```typescript
import { SQLDatabase } from "encore.dev/storage/sqldb";

const db = new SQLDatabase("my-db", {
  migrations: "./migrations",
});
```

Encore provisions PostgreSQL automatically (Docker locally, RDS/Cloud SQL in production). Write migrations in `migrations/` as numbered SQL files.

Query with tagged template literals:

```typescript
const row = await db.queryRow`SELECT * FROM users WHERE id = ${id}`;
await db.exec`INSERT INTO users (name) VALUES (${name})`;
```

## Pub/Sub

```typescript
import { Topic, Subscription } from "encore.dev/pubsub";

interface OrderEvent {
  orderId: string;
  total: number;
}

const orderCreated = new Topic<OrderEvent>("order-created", {
  deliveryGuarantee: "at-least-once",
});

// Publish
await orderCreated.publish({ orderId: "123", total: 49.99 });

// Subscribe
const _ = new Subscription(orderCreated, "process-order", {
  handler: async (event) => {
    // handle event
  },
});
```

## Cron Jobs

```typescript
import { CronJob } from "encore.dev/cron";

const _ = new CronJob("cleanup", {
  title: "Clean up old sessions",
  every: "1h",
  endpoint: cleanupEndpoint,
});
```

## Object Storage

```typescript
import { Bucket } from "encore.dev/storage/objects";

const uploads = new Bucket("uploads", { versioned: false });

// Upload
await uploads.upload("file.txt", Buffer.from("content"), {
  contentType: "text/plain",
});

// Download
const data = await uploads.download("file.txt");
```

## Caching

Encore provides Redis-backed caching via `encore.dev/storage/cache`. Define a cluster, then create typed keyspaces.

```typescript
import { CacheCluster, StringKeyspace, StructKeyspace, IntKeyspace, expireIn } from "encore.dev/storage/cache";

const cluster = new CacheCluster("my-cache", {
  evictionPolicy: "allkeys-lru",
});

// String keyspace
const tokens = new StringKeyspace<{ tokenId: string }>(cluster, {
  keyPattern: "token/:tokenId",
  defaultExpiry: expireIn(3600 * 1000),
});
await tokens.set({ tokenId: "abc" }, "value");
const val = await tokens.get({ tokenId: "abc" }); // undefined on miss

// Integer keyspace (supports increment/decrement)
const counters = new IntKeyspace<{ userId: string }>(cluster, {
  keyPattern: "requests/:userId",
  defaultExpiry: expireIn(10 * 1000),
});
const count = await counters.increment({ userId: "user123" }, 1);

// Struct keyspace (stores objects as JSON)
interface UserProfile { name: string; email: string; }
const profiles = new StructKeyspace<{ userId: string }, UserProfile>(cluster, {
  keyPattern: "profile/:userId",
  defaultExpiry: expireIn(3600 * 1000),
});
await profiles.set({ userId: "123" }, { name: "Alice", email: "alice@example.com" });
```

Available keyspace types: `StringKeyspace`, `IntKeyspace`, `FloatKeyspace`, `StructKeyspace`, `StringListKeyspace`, `NumberListKeyspace`, `StringSetKeyspace`, `NumberSetKeyspace`.

To reference a cluster from another service: `CacheCluster.named("my-cache")`.

## Secrets

```typescript
import { secret } from "encore.dev/config";

const apiKey = secret("StripeSecretKey");

// Use
const key = apiKey();
```

Set secrets via CLI: `encore secret set --dev StripeSecretKey`

## Auth

```typescript
import { authHandler } from "encore.dev/auth";
import { Gateway } from "encore.dev/api";
import { Header } from "encore.dev/api";

interface AuthParams {
  authorization: Header<"Authorization">;
}

interface AuthData {
  userID: string;
}

const handler = authHandler<AuthParams, AuthData>(async (params) => {
  // validate token, return auth data
  return { userID: "user-123" };
});

export const gateway = new Gateway({ authHandler: handler });
```

Then use `auth: true` on endpoints to require authentication.

## Key Principles

- Infrastructure is declared in application code, not in separate config files
- All imports come from `encore.dev/*` packages
- Services communicate through typed API calls, not raw HTTP
- Local development uses `encore run` which provisions real infrastructure (PostgreSQL, Redis, etc.)
- Deploy with `git push encore` or `encore build docker` for self-hosting
