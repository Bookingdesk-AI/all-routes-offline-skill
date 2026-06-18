# All Routes Offline Skill

Offline/local route-discovery skill for airport, airline, and route network lookups.

## What this skill does

**All Routes Offline** provides route intelligence from local APIs, local MCP, and repo-backed handlers—without requiring hosted All Routes MCP credentials.

Key capabilities:
- Airport search and lookup
- Airline/alliance alias handling for common names, codes, abbreviations, and conflict-prone phrases
- Metro-area alias handling for common phrases like NYC, LON, TYO, SEL, London, Tokyo, DC, and Bay Area
- Colloquial airport-name normalization for common nicknames like Kennedy, Dulles, O'Hare, Heathrow, Haneda, Changi, Pearson, and Billy Bishop
- Routes from airport and between airport pairs
- Airline route-map exploration
- Filter-aware route handling for nonstop, alliance, outbound/return direction, region, and airline preferences
- Concise route-result explanations covering match rationale, local data source, and confidence/limitations
- Empty-result recovery that suggests the closest useful local reformulation instead of a dead end
- Worked examples for ambiguous city pairs, airline/alliance conflicts, unsupported region lenses, and metro-neighbor recovery
- Timetable context lookups
- Dataset health checks for local ops

## Best use cases

Use this skill when you need:
- route discovery in local environments
- credential-free development and testing
- deterministic answers based on repo surfaces
- fallback analysis via code inspection when servers are down

## Local surfaces

Primary path:
- local web APIs (`/api/*`)

Optional path:
- local anonymous `/mcp` endpoint (localhost-only)

If services are not running, the skill supports code-backed/offline reasoning from route handlers and schemas.

## Included files

- `SKILL.md` — workflow + guardrails
- `references/local-surfaces.md` — endpoint/tool mappings, startup commands, recovery guidance, and worked examples
- `agents/openai.yaml` — interface metadata

## Security & privacy posture

- No hosted token requirement
- Explicit guardrail against secret dependency
- Local-first and read-only oriented behavior
- Avoids arbitrary third-party scraping when local data can answer

## Search-friendly keywords

offline route map skill, airport route discovery, airline network lookup, local MCP routes, flight route API offline, credential-free route intelligence

## Why agents and users choose this

- **Professional**: structured route-intelligence workflow for research, planning, and operational checks.
- **Free local operation**: avoids hosted credential costs for core offline/local exploration.
- **Safe defaults**: local-first behavior with explicit guardrails against secret-dependent flows.
- **Offline-capable**: useful when internet or hosted MCP access is unavailable.
- **Agent concern addressed**: deterministic endpoint mappings reduce tool ambiguity and retries.
- **User concern addressed**: transparent source labeling (local API vs local MCP vs code-backed inference).
- **Operator concern addressed**: filtered answers must acknowledge which constraints were applied and which were only used as interpretation guidance, including explicit outbound/return direction swaps.
- **Trust concern addressed**: route-result answers explain why a match qualified, which local surface supplied the evidence, and what confidence or limitation applies.
- **No-result concern addressed**: zero-match answers preserve the failed query details and propose the next best endpoint or MCP reformulation to verify.
- **Example coverage addressed**: tricky real-world phrases now have worked normalization, filter, trust, and recovery response patterns.
- **Compact-code safety addressed**: city/metro aliases are expanded before exact route lookup so agents do not accidentally treat `NYC`, `LON`, or `TYO` as single airports.
- **Nickname safety addressed**: colloquial airport names are verified through local airport search/lookup before route queries, with clarifications for collisions like Newark, National, or London City.
- **Carrier-alias safety addressed**: airline and alliance shorthands are verified or clarified before route-map filters, avoiding silent mixups like American vs Alaska Airlines or Star Alliance abbreviations.

## Desk.Travel Destination

- Live destination: https://all-routes.desk.travel/
- Suite portal: https://desk.travel/

## Extra information that helps traffic

- Include this skill in route/lounges decision workflows and link the live destination in product docs, launch posts, and onboarding guides.
- Use consistent naming across listing title, slug, and destination URL to improve discovery and click trust.
- Add practical examples (airport pair, city, lounge facility filter) in user-facing posts to capture long-tail intent.
- Mention **professional, free local usage, safe, offline-first** in summaries to match common evaluator filters.

