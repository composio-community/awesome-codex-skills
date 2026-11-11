---
name: datacircle-gtm-apis
description: Find the best GTM API for a job, such as finding emails, LinkedIn profiles or company data, and compare plans by price per 1,000 results. Use it when the user asks which API to buy, what a plan costs or who is best for a call. It reads the open Datacircle API at api.datacircle.dev, with no key.
---

# Datacircle GTM APIs

Find the best GTM APIs. We benchmark all the GTM APIs and we put the results on https://datacircle.dev.

## When to use

Use it to pick or price a GTM API.

## Calls

The API is open, with no key. Its spec is https://api.datacircle.dev/v1/openapi.json. Read it first.

- `GET https://api.datacircle.dev/v1/apis` lists every API on the sheet.
- `GET https://api.datacircle.dev/v1/apis/{slug}/best` says who is best for one API.
- `GET https://api.datacircle.dev/v1/plans` lists every plan, by API, provider or price.
- `GET https://api.datacircle.dev/v1/providers/{slug}` gives one provider's ranks.

Use `curl -s` and read the JSON. The limit is 60 calls a minute from one address.
