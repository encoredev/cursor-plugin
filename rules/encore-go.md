---
description: "Encore Go conventions for APIs, services, infrastructure primitives, and project structure"
---

# Encore Go Conventions

## Project Structure

Encore Go apps are organized into services. Each service is a Go package. The package name is the service name.

```
my-app/
├── service-a/
│   ├── service-a.go
│   └── migrations/
├── service-b/
│   └── service-b.go
├── go.mod
└── encore.app
```

## API Endpoints

Define endpoints using `//encore:api` annotations:

```go
package myservice

import "context"

type CreateRequest struct {
    Name string `json:"name"`
}

type CreateResponse struct {
    ID   string `json:"id"`
    Name string `json:"name"`
}

//encore:api public method=POST path=/items
func Create(ctx context.Context, req *CreateRequest) (*CreateResponse, error) {
    // implementation
}
```

Access modifiers: `public` (exposed), `private` (internal), `auth` (requires authentication).

For raw HTTP:

```go
//encore:api public raw method=POST path=/webhook
func Webhook(w http.ResponseWriter, req *http.Request) {
    // raw HTTP handler
}
```

## Databases

```go
package myservice

import "encore.dev/storage/sqldb"

var db = sqldb.NewDatabase("my-db", sqldb.DatabaseConfig{
    Migrations: "./migrations",
})

// Query
row := db.QueryRow(ctx, "SELECT * FROM users WHERE id = $1", id)
```

## Pub/Sub

```go
import "encore.dev/pubsub"

type OrderEvent struct {
    OrderID string
    Total   float64
}

var OrderCreated = pubsub.NewTopic[*OrderEvent]("order-created", pubsub.TopicConfig{
    DeliveryGuarantee: pubsub.AtLeastOnce,
})

// Publish
OrderCreated.Publish(ctx, &OrderEvent{OrderID: "123", Total: 49.99})

// Subscribe
var _ = pubsub.NewSubscription(OrderCreated, "process-order",
    pubsub.SubscriptionConfig[*OrderEvent]{
        Handler: func(ctx context.Context, event *OrderEvent) error {
            // handle
            return nil
        },
    },
)
```

## Cron Jobs

```go
import "encore.dev/cron"

var _ = cron.NewJob("cleanup", cron.JobConfig{
    Title:    "Clean up old sessions",
    Every:    1 * cron.Hour,
    Endpoint: Cleanup,
})
```

## Secrets

```go
var secrets struct {
    StripeSecretKey string
}
```

Set via CLI: `encore secret set --dev StripeSecretKey`

## Auth

```go
import "encore.dev/beta/auth"

type AuthParams struct {
    Authorization string `header:"Authorization"`
}

type AuthData struct {
    UserID string
}

//encore:authhandler
func AuthHandler(ctx context.Context, p *AuthParams) (auth.UID, *AuthData, error) {
    // validate, return user ID and auth data
}
```

## Key Principles

- APIs are annotated Go functions, not router registrations
- Infrastructure is declared as package-level variables
- All imports come from `encore.dev/*`
- Services communicate through typed function calls
- Local development uses `encore run`
- Deploy with `git push encore` or `encore build docker`
