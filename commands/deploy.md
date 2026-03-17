---
name: deploy
description: Deploy the Encore application to Encore Cloud or build a Docker image for self-hosting.
---

# Deploy Encore App

## Deploy to Encore Cloud

1. Commit all changes: `git add -A && git commit -m "description"`
2. Push to deploy: `git push encore`
3. Encore Cloud provisions infrastructure in your AWS or GCP account automatically.

## Self-host with Docker

1. Build a Docker image: `encore build docker my-app:latest`
2. Run the image with your own infrastructure configuration.
3. See https://encore.dev/docs/ts/self-host/build for details.
