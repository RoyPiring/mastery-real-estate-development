# Build a Route-Decision Model

> Inside the [Mastery Track: Real Estate Development](../../README.md) portfolio · *A hands-on route from amateur to command in real estate development, one end-to-end build at a time.*

## Overview

This build turns a career-route decision into a reproducible financial model. It compares three real estate development entry routes, a PhD, an MRED, and an Experience-plus-CCIM path, on the same four axes: sticker cost, forgone earnings, total cost, and expected outcome wage. The recommendation comes from stated assumptions in code, not gut feeling, and every route is scored against one baseline wage, the BLS May 2024 median for Construction Managers (SOC 11-9021).

The model prices time, not just tuition. Once forgone earnings are counted, the MRED becomes the cheapest route to a development role despite the highest sticker price, because tuition is a one-time cost while lost wages accumulate each year. A break-even calculator over a 50-year horizon exposed a modeling artifact: when a route had no wage premium over the baseline, it returned Never before the NPV loop even ran. A funded-PhD sensitivity pass changed the MRED outcome wage and moved it off Never, and a decision brief committed to a route while naming exactly what would reverse it.

This is one build on the route to command of real estate development. It ships a Python model with numpy-financial NPV, a manual reconciliation check, a decision brief with reversal conditions, and a teach-back that defends the call rather than only checking the numbers.

## Architecture

```mermaid
---
title: Build a Route-Decision Model
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Learner[/Learner choosing a development route/]
    Decision[/A defended route with named reversal conditions/]

    subgraph Setup["Toolchain and baseline"]
        Venv[(Python .venv: numpy-financial)]
        Config[(config.py: BASELINE_WAGE 106,980, BLS SOC 11-9021)]
        RouteModel[(route_model.py)]
        Rails[(GitHub repo and Linear ticket)]
    end

    subgraph Routes["Three entry routes"]
        PhD[(PhD)]
        MRED[(MRED)]
        ExpCCIM[(Experience plus CCIM)]
    end

    subgraph Axes["The same four axes"]
        Score(Score every route on the same axes)
        StickerCost[(Sticker cost)]
        Forgone[(Forgone earnings)]
        TotalCost[(Total cost)]
        Outcome[(Expected outcome wage)]
    end

    subgraph BreakEvenGate["Break-even and the naive mistake"]
        BreakEven(NPV break-even over 50 years)
        NeverGuard{{annual_differential <= 0 returns Never}}
    end

    subgraph Sensitivity["Sensitivity and brief"]
        Funded(Funded-PhD sensitivity: MRED wage 123,932)
        Reconcile{{Manual reconciliation of the math}}
        Brief(Decision brief with reversal conditions)
    end

    TeachBack{{Teach-back defends the call and names the flip}}

    Learner -- "wants a defensible choice" --> Score
    Config -- "one baseline for all" --> RouteModel
    Venv -- "numpy-financial NPV" --> RouteModel
    Rails -- "tracks the build" --> RouteModel
    RouteModel -- "scores" --> Score
    PhD -- "priced by" --> Score
    MRED -- "priced by" --> Score
    ExpCCIM -- "priced by" --> Score
    Score -- "tuition" --> StickerCost
    Score -- "years given up" --> Forgone
    StickerCost -- "plus time" --> TotalCost
    Forgone -- "plus time" --> TotalCost
    Score -- "median wage" --> Outcome
    TotalCost -- "against wage gap" --> BreakEven
    Outcome -- "wage differential" --> BreakEven
    BreakEven -- "no premium" --> NeverGuard
    NeverGuard -- "artifact, not a finding" --> Funded
    Funded -- "differential now 16,952" --> BreakEven
    Funded -- "checked by" --> Reconcile
    Reconcile -- "math holds" --> Brief
    BreakEven -- "PhD 18.0, MRED 31.5" --> Brief
    Brief -- "commit plus reversals" --> TeachBack
    TeachBack -- "horizon is the real flip" --> Decision
    Brief -- "recommends Experience plus CCIM" --> Decision

    class Venv,Config,RouteModel,Rails,PhD,MRED,ExpCCIM,StickerCost,Forgone,TotalCost,Outcome datastore
    class Score,BreakEven,Funded,Brief service
    class NeverGuard,Reconcile,TeachBack event
    class Learner,Decision io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/route-decision-model.md`](./documents/route-decision-model.md).

## Implementation

This system is built across **7 phases**:

1. Building a Route-Decision Model for Real Estate Development
2. Loading Published Data and Establishing the Baseline
3. Scoring All Three Routes on the Same Four Axes
4. Discovering the Naive Mistake: When Nothing Ever Pays Back
5. Flipping the Recommendation: Sensitivity Analysis and the Decision Brief
6. Documenting the Model: Topology, Guide, and Walkthrough
7. Proving Mastery: The Teach-Back Instrument

For the full walkthrough with screenshots and step-by-step content, see [`documents/route-decision-model.md`](./documents/route-decision-model.md).

## Validation

Each build phase below is documented in [`documents/route-decision-model.md`](./documents/route-decision-model.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Building a Route-Decision Model for Real Estate Development
- ✅ Loading Published Data and Establishing the Baseline
- ✅ Scoring All Three Routes on the Same Four Axes
- ✅ Discovering the Naive Mistake: When Nothing Ever Pays Back
- ✅ Flipping the Recommendation: Sensitivity Analysis and the Decision Brief
- ✅ Documenting the Model: Topology, Guide, and Walkthrough
- ✅ Proving Mastery: The Teach-Back Instrument
