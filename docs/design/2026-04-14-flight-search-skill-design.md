# Flight Search Skill Design

## Overview

A Claude Code skill (`/flight-search`) that guides users through a comprehensive flight cost optimization workflow. It collects trip details, determines which of 8 strategies apply, runs each relevant strategy using a mix of Claude's knowledge and targeted web searches, and produces a final consolidated report.

**Source inspiration:** stics.ai Instagram post -- "8 Prompts to Reduce Flight Prices by More Than 50%"

## Skill Identity

- **Name:** `flight-search`
- **Trigger:** `/flight-search`
- **Type:** Rigid (follows a defined process)
- **Implementation:** Single prompt-based skill file (no code/scripts)
- **Tools used:** WebSearch, WebFetch, Write

## Approach

**Intake + Relevance Filter:** Claude collects trip details upfront, assesses which of the 8 strategies are relevant for the specific trip, presents the plan with reasoning, allows the user to override, then executes the confirmed strategies sequentially. Each strategy builds on findings from previous ones.

**Search strategy:** Strategic -- web search for strategies that benefit from real-time data (deals, policies, fees), Claude's knowledge for general advice (date patterns, skiplagging risks, negotiation frameworks).

**Output:** Interactive conversation during the process, plus a consolidated markdown report at the end.

## Intake Phase

When invoked, Claude collects the following one question at a time:

### Required Inputs

| Input | Description |
|-------|-------------|
| Trip type | Round-trip / One-way / Multi-city |
| Origin(s) | Airport or city (Claude resolves to IATA codes) |
| Destination(s) | Airport or city |
| Target date(s) | Departure (and return for round-trip) |
| Date flexibility | Days flexible around target dates (0 = fixed) |
| Budget | Maximum spend with currency (e.g., "800 EUR") or "no hard limit, just optimize" |
| Passengers | Count and types (adult, child, infant) |

### Optional Inputs (asked if relevant)

| Input | Description |
|-------|-------------|
| Home region | For prioritizing regional carriers and loyalty programs |
| Preferred airlines / alliances | Or airlines to avoid |
| Cabin class | Economy / Premium Economy / Business / First |
| Loyalty programs | Existing memberships |
| Baggage needs | Checked bags, special equipment |
| Connection preferences | Max layover time, max stops, airports to avoid |

For multi-city trips, Claude collects origin/destination/date per leg and notes which strategies apply per-leg vs. trip-wide.

After collection, Claude summarizes the trip profile back to the user for confirmation before proceeding.

## Relevance Filter

After confirming trip details, Claude assesses which strategies apply and presents the plan.

### Filtering Logic

| Strategy | Skip when... |
|----------|-------------|
| 1. Ideal Date Scanner | Date flexibility is 0 (fixed dates) |
| 2. Hidden Flight Finder | Never skipped -- always relevant |
| 3. Route Optimizer | Direct flights are very cheap already, or user specified "direct only" |
| 4. Deals & Promotions Detector | Never skipped -- runs against airlines identified by strategy 2 if none were specified upfront |
| 5. Fee Breakdown | Never skipped -- always relevant |
| 6. Price Negotiation Email | Budget airline (no price matching programs), or user declines |
| 7. Flexibility & Risk Analysis | User says plans are 100% fixed, no chance of change |
| 8. Hidden Destination Tickets | One-way trips, short-haul domestic, user has checked bags (skiplagging incompatible) |

### Presentation

Claude presents the plan:

> "Based on your trip (ATH -> LHR, round-trip, 3 days flexible, economy, 2 passengers), I'll run these 6 strategies: [list with one-line reason each]. I'm skipping these 2: [list with why]. Want me to add any back or skip others?"

The user can override any filtering decision.

### Execution Order

Strategies are ordered so earlier ones feed into later ones:

1. **Ideal Date Scanner** -- narrows the date window
2. **Hidden Flight Finder** -- identifies airline/route candidates
3. **Route Optimizer** -- finds alternative routings
4. **Deals & Promotions Detector** -- searches for discounts on identified airlines
5. **Fee Breakdown & Elimination** -- analyzes top flight candidates
6. **Flexibility & Risk Analysis** -- compares policies of top candidates
7. **Hidden Destination Tickets** -- evaluates skiplagging on best routes
8. **Price Negotiation Email** -- drafts based on all findings

## Strategy Execution Details

Each strategy follows the same pattern: brief intro -> analysis -> findings -> conversational check-in with user.

### 1. Ideal Date Scanner

- **Knowledge:** General pricing patterns (midweek cheaper, avoiding holidays, shoulder season logic)
- **Web search:** Searches Google Flights / Skyscanner for actual price comparisons across the flexible date range
- **Output:** Top 3 date combinations with price estimates and reasoning why each is cheaper

### 2. Hidden Flight Finder

- **Knowledge:** Database of low-cost carriers by region (Ryanair, Wizz Air, IndiGo, Spirit, etc.), regional airlines often missed by aggregators
- **Web search:** Searches for flights on the specific route, including carriers not indexed by major aggregators
- **Output:** Comprehensive flight list sorted by total price (including taxes/fees), flagging lesser-known options

### 3. Route Optimizer with Smart Connections

- **Knowledge:** Hub airport knowledge, transit fee awareness (e.g., LHR high transit fees), connection time rules of thumb
- **Web search:** Searches for multi-leg pricing on alternative routing options
- **Output:** 2-3 alternative routings with estimated total cost, layover times, and transit fee warnings

### 4. Verified Deals & Promotions Detector

- **Knowledge:** General promo code patterns, airline sale calendars
- **Web search:** Actively searches airline websites, coupon aggregators, travel deal sites for current promotions on identified airlines
- **Output:** Verified active deals with source, expiration date, and estimated savings. Discards unverifiable/expired ones explicitly.

### 5. Fee Breakdown & Elimination

- **Knowledge:** General airline fee structures, common avoidance strategies (credit card perks, status benefits, packing hacks)
- **Web search:** Searches current fee schedules for the specific airlines/fare classes in play
- **Output:** Table of fees per candidate flight (baggage, seat selection, boarding, meals) with specific strategies to avoid or reduce each

### 6. Price Negotiation Email

- **Knowledge:** Persuasion frameworks, price-match policy knowledge, email structure best practices
- **Web search:** Searches for the airline's specific price-match or best-price-guarantee policies
- **Output:** Ready-to-send email draft referencing the user's loyalty status, competitor prices found, and applicable policies

### 7. Flexibility & Risk Analysis

- **Knowledge:** General fare class rules (basic economy vs. flex), risk assessment frameworks
- **Web search:** Searches for specific change/cancellation policies and fees for the candidate flights
- **Output:** Risk comparison table across candidate flights, highlighting hidden clauses (e.g., "no changes within 24h of departure", "credit only, no refund")

### 8. Hidden Destination Tickets (Skiplagging)

- **Knowledge:** How skiplagging works, risks (banned by some airlines, can't check bags, loyalty account risks, return leg cancellation), legal status
- **Web search:** Searches for pricing to cities beyond the destination where the user's city is a layover
- **Output:** Viable skiplagging options with savings estimate, plus clear risk/reward assessment and specific airline policies on the practice

## Web Search Targets

| Strategy | Search targets |
|----------|---------------|
| 1. Dates | Google Flights, Skyscanner, Kayak |
| 2. Flights | Google Flights, airline direct sites, low-cost carrier sites |
| 3. Routes | Google Flights, Rome2Rio, Kiwi.com |
| 4. Deals | Airline promo pages, SecretFlying, TheFlightDeal, coupon sites |
| 5. Fees | Airline baggage/fee pages directly |
| 6. Negotiation | Airline price-match policy pages |
| 7. Risk | Airline fare rules, cancellation policy pages |
| 8. Skiplagging | Skiplagged.com, Google Flights for beyond-destination pricing |

## Conversation Flow

The skill defines a strict state machine:

```
INTAKE -> CONFIRM_TRIP -> RELEVANCE_FILTER -> CONFIRM_PLAN
  -> STRATEGY_1 -> ... -> STRATEGY_N -> REPORT -> DONE
```

Each state transition includes a user check-in. Claude cannot skip ahead or combine strategies without user acknowledgment.

## Final Report

After all strategies complete, Claude generates a consolidated markdown report.

**File location:** `reports/flight-search-YYYY-MM-DD-{origin}-{destination}.md`

### Report Structure

```
# Flight Search Report

## Trip Profile
- Route, dates, passengers, cabin, budget, preferences

## Executive Summary
- Best overall recommendation with estimated savings
- Quick-win actions (things to do right now)

## Strategy Results

### 1. Date Analysis
[Findings from strategy 1]

### 2. Flight Options
[Findings from strategy 2]

... (only strategies that were executed)

## Recommended Action Plan
1. Prioritized step-by-step actions
2. Time-sensitive items flagged (e.g., "promo expires Apr 18")
3. Links to booking pages where available

## Risk Summary
- Comparison table of top 2-3 flight options
- Risk score per option (change flexibility, cancellation, hidden fees)

## Appendix
- Price Negotiation Email (ready to copy/paste)
- Fee comparison table
- Skiplagging analysis (if applicable)
```

### Report Principles

- The report is a reference document, not a transcript of the conversation
- Each section is self-contained and actionable
- Time-sensitive items (promos, sales) are clearly flagged with dates
- The executive summary alone should give enough info to act on

## Error Handling

- If a web search returns no useful results, Claude states what it searched, what it found (nothing), and falls back to knowledge-based advice for that strategy
- If a strategy produces no actionable findings, Claude says so clearly rather than padding the output
- Never fabricates prices, promo codes, or policy details

## Trip Type Handling

- **Round-trip:** All 8 strategies apply normally
- **One-way:** Strategy 8 (skiplagging) is auto-skipped; date scanner analyzes departure only
- **Multi-city:** Strategies apply per-leg where relevant (dates, flights, routes) and trip-wide where appropriate (deals, negotiation email). Claude tracks findings per leg and consolidates in the report.
