# Flight Assistant

A Claude Code skill for comprehensive flight cost optimization.

## Skills

<flight-search>
    <objective>
        Guides users through 8 flight cost optimization strategies to reduce ticket prices by analyzing dates, routes, airlines, fees, deals, and policies.
    </objective>
    <command>
        /flight-search
    </command>
    <info>
        Invoke with /flight-search to start a guided flight optimization session.

        The skill collects trip details (origin, destination, dates, budget, preferences),
        determines which of 8 strategies are relevant, then executes them sequentially:

        1. Ideal Date Scanner - finds cheapest date combinations
        2. Hidden Flight Finder - discovers lesser-known airlines and routes
        3. Route Optimizer - designs alternative multi-stop routings
        4. Deals & Promotions Detector - finds active promo codes and sales
        5. Fee Breakdown & Elimination - analyzes and reduces hidden fees
        6. Flexibility & Risk Analysis - compares change/cancellation policies
        7. Hidden Destination Tickets - evaluates skiplagging opportunities
        8. Price Negotiation Email - drafts price-match request emails

        Supports round-trip, one-way, and multi-city trips worldwide.
        Uses strategic web search for real-time data (prices, deals, policies).
        Produces a consolidated report saved to reports/ directory.

        No parameters required -- the skill guides you through input collection.

        Example:
          /flight-search
          (then follow the prompts: trip type, origin, destination, dates, etc.)
    </info>
</flight-search>
