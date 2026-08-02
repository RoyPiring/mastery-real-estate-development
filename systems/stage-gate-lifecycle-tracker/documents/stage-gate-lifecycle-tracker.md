<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Stage-Gate Lifecycle Tracker

**Project Link:** [View Project](https://nextwork.ai/projects/506eabe0-a709-42aa-9a58-a7cd6a8a98ba)

**Author:** Roy Piring Jr: Sr. Cloud Engineer | Architect  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_elcfyn9m)

## Committing to the Build

### Why a tracker that kills deals matters

In this step, I set up the development environment and architecture for the tracker. The goal was to turn the nine real estate development gates into a functional decision system instead of leaving them as a static framework.

The tracker mattered because stage-gate discipline is only useful when it can stop a bad deal. A system that only describes the lifecycle does not protect capital, time, or execution focus.

The build focused on making each gate testable. That meant each stage needed fields, thresholds, and a clear GO or KILL outcome that could be evaluated by code.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_lfmlhby8)

## Designing the Agent System and Encoding Nine Gates

### Design-before-build: agents, topology, and decision records

In this step, I documented the agent system design before building the tracker. The decision records defined the agent roles, the lifecycle topology, and the logic that would govern how deals moved through the gates.

I also defined the nine development stages and the fields each gate needed to evaluate. That gave the tracker a consistent structure before any Streamlit display or deal runner logic was added.

The first agents were built to show the tracker in Streamlit. That gave the system a visible interface while keeping the gate rules grounded in the design records.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_f0ibbgia)

### The Financing kill trigger and why the broken deal targets it

The Financing kill trigger was DSCR below 1.20x on committed loan terms, or an equity gap greater than 20 percent of the total equity requirement.

Financing was the right gate to target because it sits just before Construction. That is the point where money starts burning in a more permanent way, so a kill there proves the tracker can stop a weak deal before the cost gets much harder to unwind.

It also tests the short-circuit behavior in the middle of the lifecycle. Since Financing is stage 6 of 9, a KILL there should prevent stages 7 through 9 from evaluating. One honest limit remained: no broken deal existed yet at that point, so the Financing target was design intent and a recommendation to confirm, not a fact already present in code.

## Proving the Tracker Advances a Good Deal

### Running a passing deal through all nine gates

In this step, I built the DealRunner agent and defined a passing deal. The test deal was a healthy 120-unit development meant to return GO across all nine gates.

The purpose was to prove the tracker could evaluate a complete deal from start to finish without stopping early. Each stage had to receive the required fields, apply its rule, and return a passing decision.

This passing case established the happy path. It showed that a healthy deal could move through the full lifecycle when every gate cleared.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_iilcmzwj)

### Why short-circuit evaluation mirrors real gate discipline

Short-circuit evaluation matched how real development decisions work. A deal that fails Entitlement does not move to Construction in real life, so the tracker should not fabricate later decisions after a kill.

It also preserved cost discipline. The point of a stage-gate system is to stop bad deals before expensive stages spend money, and a runner that keeps evaluating after a kill would contradict that purpose.

The trade-off was documented under Honest Limits in docs/DECISIONS.md. A deal killed at stage 3 is never tested against stages 4 through 9, so fixing that first issue may reveal another kill later. That is why app.py checked len(log) == len(STAGES) instead of treating an all-GO log alone as proof the deal finished.

## Proving the Tracker Stops a Broken Deal

### Engineering a deal to fail at the right gate

In this step, I added the Grayfield Tower deal to test the Financing kill trigger. The goal was to confirm that a broken deal could reach Financing, fail there, and stop without evaluating the later stages.

I also updated the Streamlit app so the broken deal could be run through the same tracker interface as the passing deal. That let the system show both a full GO path and a controlled KILL path.

The coverage checker verified that all nine stages and their required fields were represented. That kept the tracker from passing tests while leaving a lifecycle stage or required field untested.

### Why the negative test is the real proof

A passing deal only proves that the tracker can say yes. Nine GO results could come from a real gate system, or from a broken tracker that always returns GO.

The broken deal is the real proof because it exercises the kill branch. It is the path that hits the triggered condition, returns early, and proves that Construction, Lease-Up, and Disposition do not render after Financing fails.

A gate that never kills is not a gate. Grayfield Tower proved the tracker could stop a bad deal instead of only describing why good deals advance.

## Teaching Back, Mapping the Architecture, and Shipping the Guide

### Teach-back instrument, topology diagram, and plain-language guide

In this step, I finalized the tracker by adding the TeachBackScorer agent, creating the topology diagram, and drafting the plain-language guide. These artifacts turned the tracker from working code into a system that could be explained and reviewed.

The topology diagram showed how the agents, stages, deals, and scoring logic connected. The guide explained the tracker in plain language so the lifecycle logic could be understood without reading the code first.

This mattered because decision tools need more than execution. They need a way to teach the logic, explain the architecture, and show why each gate exists.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_9jczomfr)

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_zgkj6437)

### How the TeachBackScorer evaluates learner reasoning

The TeachBackScorer evaluated each learner response with two checks. has_decision checked whether the answer included a decision word such as go, kill, stop, advance, proceed, reject, or pass. has_kill_ref checked whether the answer included any of the first five words from that gate’s kill_trigger.

Each response could return PASS, PARTIAL, or FAIL. PASS meant both checks were true. PARTIAL meant a decision was named but the kill trigger was not referenced. FAIL meant the field was blank or had no decision word.

The per-gate results rolled up into an overall score. PASS required at least 7 gates passed, PARTIAL required at least 5, and anything below that returned FAIL. The scoring was intentionally simple substring matching, but that created leniency, such as the inside there counting as a kill-trigger reference.

## Two-Audience Walkthrough and Lessons Learned

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/506eabe0-a709-42aa-9a58-a7cd6a8a98ba_tburxki4)

### Reflecting on the hardest gate to encode

Construction was the hardest gate to encode. The problem was that every other gate had a real walk-away decision behind it, while Construction often does not.

Once steel is in the ground, the realistic choices are usually restructure or absorb the loss. The deal does not simply stop in the same way it can before financing or entitlement.

The kill trigger kept turning into a financing test. Cost to complete exceeding remaining loan, equity, and contingency describes a funding crisis, not a clean stop-or-continue decision. Disposition was also difficult because a failed disposition test often means hold, while the tracker only supported GO and KILL. Those lessons drove the recommendation in LESSONS.md to give DealRunner a third verdict so a gate can fail without pretending the whole deal died.

## Tools, Concepts, and Reflections

### Key tools and concepts from the build

The key tools I used included Python for the decision logic, Streamlit for the user interface, Cursor as the development environment, GitHub for version control, Linear for delivery tracking, and Mermaid for system topology.

The main concepts I learned included encoding a real estate stage-gate lifecycle as deterministic rules, using short-circuit logic to stop broken deals, and using negative testing to prove the tracker can find failure points.

The larger lesson was that a lifecycle tracker needs to do more than display stages. It needs objective thresholds, clear outcomes, and enough honesty to show where GO and KILL are too limited for real development decisions.

### Time and challenges

This build took me approximately 60 minutes. That time covered the environment setup, nine-gate design, Streamlit tracker, good deal run, broken deal run, coverage checker, teach-back scoring, topology diagram, guide, and lessons learned.

The hardest part was defining kill triggers that were objective without becoming too rigid. Real estate risks can be subjective, but the tracker needed thresholds a program could test.

In my lessons learned, Financing appeared as the hardest criteria to write because it required balancing lender requirements with deal-specific financial data. I would likely make the debt-yield thresholds more conservative to account for market volatility.

### What this project delivered

I completed this build to learn how to operationalize the real estate development lifecycle as a deterministic, rule-based tracker. The system converted nine development stages into gates that could advance a healthy deal or stop a broken one.

The most useful part was engineering a broken deal to test a specific kill trigger. That showed how to build a failure-aware system instead of only proving the happy path.

Next, I want to connect this type of decision-support tool to external data sources, such as market metrics or regulatory databases. That would let the tracker validate kill triggers with live evidence instead of depending only on manual inputs.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/506eabe0-a709-42aa-9a58-a7cd6a8a98ba)*
