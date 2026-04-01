---
title: 'Oil Tankers Tracking'
date: 2026-04-02T19:08:56+13:00
draft: false
showToc: true
params:
  ShareButtons: ["linkedin", "reddit"]
---

## Tracking oil tankers bound for New Zealand

This project started as a practical way to keep an eye on oil tankers that are either heading to recognised New Zealand destinations or are already in, or have recently arrived at, Marsden Point. Instead of building a large tracking platform, the idea here is to keep the scope narrow: collect the vessel data that matters, classify it, store a daily snapshot, and expose it through a small API.

The codebase itself is built as a Node.js and TypeScript service. It uses Fastify for the API layer, Zod for configuration validation, and libSQL for persistence, with Turso as the natural remote database option. For local development the project can run entirely against a file-backed database and a mock provider, which makes it easy to test the full pipeline without depending on external APIs.

## How the data flow works

The project also doesn't just collect the status of the ships, but also decides which vessels should actually be included. The refresh job pulls inbound vessels and recent arrivals from the configured provider, deduplicates overlapping results, runs each record through an oil-tanker classifier, and then persists the final snapshot. That keeps the output focused on crude and product tankers rather than every ship moving through the region.

There is also a layer of domain-specific cleanup around destinations and vessel status. Port name variants are normalised so messy AIS destination strings still resolve to the right place, and each vessel is assigned a status such as `inbound`, `in_port`, or `arrived_recently`. That combination of classification and normalisation is what turns raw AIS data into something useful instead of a noisy feed. Interstingly, the crew operating the vessel can insert whatever information they want under some of the AIS fields, which makes the process of filtering more interesting.

## Provider and deployment model

The service is built around a provider interface, which means the domain logic does not need to know whether the data comes from a mock feed, AISstream, or a future provider. At the moment the production-oriented path is the AISstream integration, which uses bounded collection windows over New Zealand waters and the Marsden Point area to gather live AIS messages and map them into the internal vessel model.

On the delivery side, the API is designed to be simple to deploy. The project includes a standard long-running server entry point as well as a separate container creation under ghcr.io, that is then pulled into my Podman container infrastructure.
The data refresh is handled independently by a GitHub Actions workflow that runs once per day on a cron schedule and can also be triggered manually when needed. That separation matters because the refresh pipeline can take longer than a short-lived serverless request, so GitHub Actions handles the collection and persistence work while the API serves the latest stored snapshot.

## Technology notes

- [Node.js](https://nodejs.org/) is the runtime that executes the API service, refresh job, and the rest of the application code.
- [TypeScript](https://www.typescriptlang.org/) provides the type-safe structure around the domain logic, provider integrations, and API layers.
- [Fastify](https://fastify.dev/) is the web framework used to expose the HTTP endpoints that serve the latest vessel snapshot.
- [Zod](https://zod.dev/) is used to validate configuration and keep environment-driven settings explicit and predictable.
- [libSQL](https://turso.tech/libsql) is the SQLite-compatible database layer used for storing the vessel snapshots.
- [Turso](https://turso.tech/) is the remote database option behind that libSQL-compatible storage setup.
- [AISstream](https://aisstream.io/) is the live AIS data source used to collect vessel movements and destination information.
- [GitHub Actions](https://github.com/features/actions) runs the scheduled refresh workflow and also supports manual refresh runs when needed.
- [Podman](https://podman.io/) is the container runtime I use to pull and run the built image in my own infrastructure.


