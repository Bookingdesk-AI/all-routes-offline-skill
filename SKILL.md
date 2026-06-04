---
name: all-routes-offline
description: Use local All Routes APIs, repo-backed handlers, and optional local MCP for airport, airline, route-map, timetable-context, and dataset-health lookups without hosted credentials. Use when route discovery must stay grounded in this repo and work without the hosted All Routes MCP server.
version: 1.3.25
---

# All Routes Offline

Use this skill when the task needs route-discovery data grounded in this repo without depending on hosted All Routes MCP credentials.

## Quick Start

1. Prefer local repo surfaces over hosted services or third-party browsing.
2. If the web app is available locally, use its `/api/*` endpoints first.
3. If the worker is available locally, you may use the local anonymous `/mcp` endpoint as an optional secondary path.
4. Normalize common airport, city, airline, and alliance phrasing before choosing an endpoint; ask a targeted clarifying question when the normalized query maps to multiple plausible entities.
5. Extract route filters such as nonstop, alliance, region, and direction before calling endpoints, then acknowledge the filters that were actually applied.
6. For route results, include a concise trust note: why the route matched, which local surface/data was used, and any confidence or limitation that affects the answer.
7. When a route search returns no results, recover with the closest useful reformulation or neighboring search instead of ending at a dead result.
8. If neither server is running, inspect the repo-backed handlers and schemas directly and label the answer as code-backed/offline rather than live endpoint output.
9. Read `references/local-surfaces.md` for concrete endpoint mappings, query-normalization guidance, filter handling, explanation notes, empty-result recovery, and startup commands.

## Workflow

### 1) Choose the narrowest local surface

- `airport search / lookup`: use the local airport API surfaces.
- `routes from airport / city pair`: use the local routes API.
- `airline route map`: use the airline routes API.
- `timetable context`: use the timetables API.
- `dataset health`: use the data health API, and treat it as an ops/debug surface.

Before calling an endpoint, translate common user phrasing into the narrowest stable identifiers:

- Airport/city: prefer exact IATA/ICAO codes when supplied; otherwise search the city/airport phrase and preserve ambiguity instead of guessing.
- Airline: normalize airline names, two-letter IATA, and three-letter ICAO codes into the airline lookup or route-map surfaces.
- Alliance: map common alliance phrases to the API-supported alliance filter values and say when no alliance filter was applied.
- Ambiguous phrases: return a short clarification prompt with the top plausible interpretations and the endpoint you would call for each.

### 2) Apply route filters explicitly

Translate user constraints into supported query parameters when possible, and keep unsupported preferences visible instead of silently dropping them:

- Nonstop/direct preference: set `maxStops=0`; if the user says connections are acceptable, omit the nonstop constraint or use the broadest supported stops behavior.
- Alliance preference: set `alliance=star`, `alliance=oneworld`, `alliance=skyteam`, or `alliance=all` after normalization.
- Region preference: apply it as a result-summary or follow-up narrowing criterion unless the local endpoint exposes a region parameter; say when it was used as post-filter guidance rather than an API filter.
- Route direction: preserve `origin` and `dest` exactly; for reverse-route requests, swap the endpoint parameters instead of describing the same query backwards.
- Airline + route filters: for airline route maps, use the carrier route-map endpoint first and describe any route, region, or nonstop preference as the applied narrowing lens.

In every filtered answer, include one compact line such as: `Applied filters: origin=LAX, dest=JFK, nonstop=maxStops=0, alliance=oneworld; region preference noted but not available as an endpoint filter.`

### 3) Explain route matches with trust context

For route-result answers, add a compact explanation block after the data summary so the operator can tell why the answer is trustworthy:

- **Why matched:** name the normalized origin/destination, carrier, alliance, nonstop, direction, or route-map condition that caused the result to qualify.
- **Data used:** state whether the answer came from a local API endpoint, local MCP tool/resource, or offline repo inspection.
- **Confidence / limitation:** give a short confidence note based on the source, such as live local endpoint output, code-backed inference, ambiguous city resolution, unsupported region filtering, or missing timetable context.

Recommended line format: `Why this matched: LAX→JFK matched the normalized airport-pair query with maxStops=0 and alliance=oneworld. Data used: local /api/routes response. Confidence: high for route presence; timetable frequency not checked.`

### 4) Recover from empty results

When a local route endpoint returns zero matches, keep the answer useful and bounded:

- First, state the exact normalized query and filters that produced no results.
- Then suggest one closest successful reformulation to try next, such as relaxing `maxStops=0`, switching `alliance=all`, using the nearest resolved airport in a multi-airport city, reversing direction, or moving from an airline route map to a broader airport-pair query.
- If the query involved an ambiguous city or unsupported region lens, suggest the most precise airport-code reformulation rather than inventing a result.
- Do not present suggested reformulations as confirmed routes until a local API, local MCP tool, or repo-backed inspection verifies them.

Recommended line format: `No exact local result for HND→SIN with maxStops=0 and alliance=star. Closest next search: relax nonstop to any stops on /api/routes?origin=HND&dest=SIN&alliance=star, or try NRT→SIN if Tokyo was meant as the metro area.`

### 5) Prefer the no-worker path

- The primary path is local web APIs and repo-backed handlers, not hosted MCP.
- If you need live local responses, start the web app with `pnpm --filter @all-routes/web dev`.
- If the app is not running, inspect the local route handlers and shared schemas instead of inventing undocumented requests.

### 6) Use local MCP only when helpful

- If the worker is already running, or the task clearly benefits from MCP tools/resources, use the local `/mcp` endpoint.
- Local anonymous MCP is intended for localhost flows only; do not assume hosted credentials or `ALL_ROUTES_MCP_TOKEN`.
- Keep MCP requests narrow and prefer exact airport or airline codes when possible.

### 7) Stay read-only and grounded

- Do not require hosted secrets.
- Do not scrape arbitrary third-party sites.
- Do not invent write actions, browser automation, or remote fetch flows around this skill.
- Prefer exact IATA, ICAO, or airline codes over fuzzy queries when the user can provide them.
- Always say whether the answer came from local MCP, a local API, or offline code inspection.

### 8) Use prompts and explanations carefully

- When local MCP is available, `plan_route_options` and `explain_route_coverage` are valid explanation surfaces.
- Without MCP, explain route coverage using the local API response or code-backed repository behavior instead of pretending a prompt tool exists.

## Reference File

- Read `references/local-surfaces.md` for local endpoint mappings, startup commands, and fallback guidance.
