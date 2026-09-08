# KAMOTO

> Autonomous market intelligence for onchain and traditional assets.

KAMOTO is an open-source AI market agent designed to research assets, interpret market data, construct trade ideas, and interact with supported trading infrastructure through modular adapters.

The goal is simple:

**give an AI agent the ability to understand markets, form a thesis, and execute programmable actions around that thesis.**

---

## What KAMOTO Does

KAMOTO combines:

* AI reasoning
* market data
* portfolio awareness
* onchain execution
* brokerage adapters
* automated research
* configurable risk controls

The agent can monitor assets, analyze market conditions, compare opportunities, generate investment theses, and produce executable trade instructions.

KAMOTO is designed as an experimentation layer for autonomous finance.

---

## Example

Tell KAMOTO:

```text
Research NVIDIA.

Analyze the current market structure, recent momentum,
risk/reward, and relevant catalysts.

If the thesis is strong enough, construct an investment
strategy using the connected execution adapters.
```

KAMOTO then creates an agent workflow:

```text
Research
   ↓
Market Data
   ↓
Thesis
   ↓
Risk Analysis
   ↓
Position Construction
   ↓
Execution Adapter
   ↓
Monitoring
```

---

## Architecture

```text
                    ┌────────────────────┐
                    │       KAMOTO       │
                    │   Reasoning Core   │
                    └─────────┬──────────┘
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
          ▼                   ▼                   ▼
   Market Research      Portfolio Engine     Risk Engine
          │                   │                   │
          └───────────────────┼───────────────────┘
                              │
                              ▼
                    Execution Router
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
          Onchain        Brokerage API     Simulation
```

---

## Features

### Autonomous Research

KAMOTO can gather structured information about an asset and build a market thesis from multiple signals.

### Market Reasoning

Instead of reacting to a single indicator, the agent evaluates:

* price structure
* momentum
* volatility
* liquidity
* market regime
* portfolio exposure
* risk/reward

### Portfolio Context

Every decision can be evaluated against the current portfolio rather than treating trades independently.

### Execution Adapters

Execution is modular.

Adapters can connect KAMOTO to:

* onchain protocols
* decentralized exchanges
* brokerage APIs
* paper-trading environments
* custom market infrastructure

### Agent Memory

KAMOTO can preserve previous theses, decisions, observations, and position history so future decisions have context.

### Risk Controls

Execution can be restricted by configurable policies:

```yaml
risk:
  max_position_size: 0.10
  max_daily_loss: 0.03
  require_confirmation_above: 5000
  allowed_assets:
    - NVDA
    - BTC
    - ETH
```

---

## Quick Start

Clone the repository:

```bash
git clone https://github.com/natoshi-kamoto/kamoto.git
cd kamoto
```

Install dependencies:

```bash
npm install
```

Create your environment file:

```bash
cp .env.example .env
```

Add your model and market-data credentials.

Then run:

```bash
npm run agent
```

---

## Agent Example

```ts
import { Kamoto } from "./src";

const agent = new Kamoto({
  model: "your-model",
  execution: "simulation",
});

await agent.run(`
  Analyze NVIDIA.

  Build a thesis using momentum, volatility,
  market structure and portfolio exposure.

  Only propose a position if expected upside
  sufficiently outweighs downside risk.
`);
```

---

## Supported Modules

```text
/core
    reasoning
    memory
    planning

/markets
    equities
    crypto
    onchain

/execution
    simulation
    brokerage
    dex

/risk
    exposure
    sizing
    limits

/data
    prices
    news
    fundamentals
```

The project is intentionally modular.

New market sources and execution environments can be added without changing the core agent.

---

## Experimental: Agent-Directed Markets

One of the experiments being explored with KAMOTO is allowing the agent to create or interact with tokenized market primitives based on its own investment thesis.

Example:

```text
Human:
Research NVIDIA and construct an onchain strategy around your thesis.

KAMOTO:
→ analyzes NVDA
→ builds thesis
→ determines exposure
→ creates strategy parameters
→ routes the strategy through an onchain market
→ monitors the position
```

This makes it possible to experiment with markets where the agent is not only analyzing assets, but actively constructing programmable financial positions around them.

---

## Philosophy

Most trading software waits for humans to tell it exactly what to do.

KAMOTO is built around a different model:

```text
observe
reason
decide
execute
learn
```

The long-term goal is to explore what happens when financial software becomes an active market participant rather than a passive interface.

---

## Roadmap

* [x] Agent reasoning core
* [x] Market-data abstraction
* [x] Portfolio context
* [x] Simulation engine
* [x] Onchain execution
* [ ] Additional brokerage adapters
* [ ] Multi-agent research
* [ ] Strategy marketplace
* [ ] Autonomous portfolio management
* [ ] Agent-created onchain markets

---

## Disclaimer

KAMOTO is experimental open-source software.

It is not financial advice and should not be treated as an autonomous financial advisor. Execution modules should be used with appropriate safeguards, permissions, and risk limits.

KAMOTO is not affiliated with NVIDIA or any brokerage, exchange, or financial institution unless explicitly stated by the respective organization.

---

## License

MIT

---

Built for people exploring the intersection of:

**AI × markets × autonomous agents × onchain finance**
