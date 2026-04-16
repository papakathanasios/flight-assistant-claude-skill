# Flight Assistant - Claude Code Skill

A Claude Code skill for comprehensive flight cost optimization. It guides you through 8 AI-driven strategies to find the cheapest possible flights using real-time web search data.

## What It Does

When invoked, the skill runs an interactive session that collects your trip details and then systematically applies up to 8 optimization strategies:

| # | Strategy | What It Finds |
|---|----------|---------------|
| 1 | **Ideal Date Scanner** | Cheapest departure/return date combinations within your flexible range |
| 2 | **Hidden Flight Finder** | Lesser-known airlines and routes you might miss on major booking sites |
| 3 | **Route Optimizer** | Alternative multi-stop routings that can significantly reduce cost |
| 4 | **Deals & Promotions Detector** | Active promo codes, sales, and seasonal discounts |
| 5 | **Fee Breakdown & Elimination** | Hidden fees analysis and strategies to reduce total cost |
| 6 | **Flexibility & Risk Analysis** | Change/cancellation policy comparison across candidates |
| 7 | **Hidden Destination Tickets** | Skiplagging opportunities on international long-haul routes |
| 8 | **Price Negotiation Email** | Drafts price-match request emails based on all findings |

The skill intelligently filters which strategies apply to your specific trip -- for example, it skips the date scanner if your dates are fixed, and skips skiplagging if you need checked bags.

## Supported Trip Types

- Round-trip
- One-way
- Multi-city

## How It Works

1. **Intake** -- Collects trip type, origin, destination, dates, flexibility, budget, passengers, and optional preferences (airline, cabin class, loyalty programs, baggage needs)
2. **Relevance Filter** -- Determines which of the 8 strategies apply to your trip
3. **Strategy Execution** -- Runs each relevant strategy sequentially, using web search for real-time pricing and deal data
4. **Report Generation** -- Produces a consolidated analysis saved to the `reports/` directory

## Installation

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and configured

### Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/papakathanasios/flight-assistant-claude-skill.git
   ```

2. Copy the skill to your Claude Code skills directory:
   ```bash
   cp -r flight-assistant-claude-skill/skills/flight-search ~/.claude/skills/
   ```

3. The skill is now available in any Claude Code session.

## Usage

In any Claude Code session, type:

```
/flight-search
```

Then follow the interactive prompts. The skill will ask you about your trip details and guide you through the optimization process.

### Output

Each search produces a detailed report saved to `reports/flight-search-YYYY-MM-DD-ORIGIN-DEST.md` containing findings from all executed strategies, pricing comparisons, and actionable recommendations.

## Project Structure

```
flight-assistant/
├── skills/
│   └── flight-search/
│       └── SKILL.md            # The skill definition (670 lines)
├── docs/
│   └── design/                 # Design documents and implementation plans
├── reports/                    # Generated flight search reports (gitignored)
├── CLAUDE.md                   # Project instructions for Claude Code
├── LICENSE                     # MIT License
└── README.md
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
