<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Route-Decision Model

**Project Link:** [View Project](https://nextwork.ai/projects/56f31de5-d9ab-4f61-a174-a87453f3a685)

**Author:** Roy Piring Jr: Sr. Cloud Engineer | Architect  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_0zvrqbv1)

## Building a Route-Decision Model for Real Estate Development

### Project goals and motivation

In this build, I created a route-decision model that compares PhD, MRED, and Experience+CCIM paths using the same financial inputs. The goal was to move beyond gut feeling and make the trade-offs visible through reproducible analysis.

The model compares program cost, opportunity cost, expected wage outcomes, and break-even timing. That makes each route easier to defend because the recommendation comes from stated assumptions instead of preference.

This mattered because education decisions can look cheaper than they are when time is ignored. The model prices both cash cost and earnings given up while enrolled, then uses those numbers to produce a recommendation and reversal conditions.

### Setting up the environment and toolchain

In this step, I set up the development environment and built the initial data-loading agent. The goal was to create a repeatable base for modeling the three real estate entry routes.

The setup gave the model a controlled place to load published data, define constants, and run the scoring logic. That made the analysis easier to rebuild and inspect.

This mattered because a career decision model should not depend on hidden spreadsheet logic. The toolchain made the assumptions visible in code and kept every route tied to the same baseline.

## Loading Published Data and Establishing the Baseline

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_hyu6j0c6)

### Understanding the baseline wage assumption

BASELINE_WAGE = 106_980 represented the opportunity cost. It measured the salary given up each year while enrolled in a program instead of working.

The value came from the BLS Occupational Employment and Wage Statistics median annual wage for Construction Managers, SOC 11-9021, from May 2024. I used it as a proxy for what the learner could earn without taking the credential path.

The value was defined once in config.py and imported into route_model.py. That kept every route scored against the same forgone-wage baseline, and it also made the value double as the outcome wage for the MRED and Experience+CCIM routes in the base case.

## Scoring All Three Routes on the Same Four Axes

### Why same-axes scoring matters

The same-axes scoring step forced all three routes to use the same comparison frame. Each path was measured by sticker cost, forgone earnings, total cost, and expected outcome.

That mattered because comparing one route by tuition and another by career upside would distort the decision. The model had to price each route under the same rules.

The BASELINE_WAGE constant made the opportunity-cost side consistent. It used the BLS May 2024 median wage for Construction Managers, SOC 11-9021, as the proxy for development-adjacent earning power.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_ukuwiy6x)

### What forgone earnings reveal about true cost

Tuition alone made the PhD and MRED look equal at $98,662, while Experience+CCIM looked almost free at $8,286. That ranking ignored time.

forgone_earnings priced the years themselves. The PhD gave up $534,900 across 5 years, while the MRED gave up $160,470 across 1.5 years. That means two routes with the same tuition differed by about $374k once time was priced. Experience+CCIM also carried $427,920 in forgone earnings across 4 years.

The ranking changed once opportunity cost was included. MRED became the cheapest route at about $259k all-in, despite having the highest sticker price, because tuition was a one-time cost while lost wages accumulated each year.

## Discovering the Naive Mistake: When Nothing Ever Pays Back

### Adding the break-even calculator

In this step, I added a break-even function to calculate the payback period for each degree route against the experience-based baseline. The model tested whether any route broke even inside a 50-year horizon.

The function compared each route’s extra cost against its annual wage difference. If the wage difference was zero or negative, the route could not repay and returned Never.

This mattered because the first model exposed a flaw in the assumptions. If a route gets no wage premium over the baseline, no amount of time or discounting can make it pay back

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_6fwh2ce7)

### Why the base case shows Never

MRED’s expected_outcome was $106,980, which matched Experience+CCIM exactly. That happened because Decision 2 assigned both routes the same BLS code, SOC 11-9021, Construction Managers.

The annual differential was therefore 0. The model hit the if annual_differential <= 0 guard and returned Never before the NPV loop ran. The $98,662 tuition and $160,470 forgone earnings were never evaluated because the route had no modeled earnings premium.

That was a modeling artifact, not a real-world finding. The base model gave MRED no wage premium, so it could not capture access, speed, or network effects that may matter outside a median-wage-differential model.

## Flipping the Recommendation: Sensitivity Analysis and the Decision Brief

### Adding the funded PhD sensitivity and brief writer

I accounted for a fully funded PhD and a higher MRED outcome wage.

I also built a brief writer that turned the model output into a formal decision brief with reversal conditions. That made the recommendation easier to defend because the brief stated what would change the answer.

The manual reconciliation check confirmed that the MRED calculations lined up with the expected values. That kept the model from hiding a math error behind a clean-looking recommendation.

### What the funded sensitivity reveals

The funded sensitivity still recommended Experience+CCIM. The recommendation did not change because neither degree repaid inside the 10-year horizon. The PhD broke even at 18.0, and the MRED broke even at 31.5.

MRED escaped Never because the funded loader changed its outcome wage from the BLS median $106,980 to the USC-reported $123,932. That changed the annual differential from $0 to $16,952, so the annual_differential <= 0 guard no longer stopped the model.

The cost side did not change. MRED still carried $98,662 in tuition and $160,470 in forgone earnings. It took thirty discounted years of $16,952, plus the 1.5 program years, to clear the $259,132 investment and produce the 31.5 break-even result.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_55ebao1i)

## Documenting the Model: Topology, Guide, and Walkthrough

### Why documentation makes the model reusable

In this step, I documented the model so the decision system could be reused and reviewed. The Mermaid topology diagram showed the model architecture and data flow.

The plain-language guide explained how to maintain and rebuild the model. That mattered because the model’s value depends on being able to update assumptions without losing the logic.

The two-audience walkthrough captured the key lessons for both technical and decision-making readers. After the documentation was complete, the changes were committed and the corresponding Linear ticket was closed.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_28ogmdkk)

### Three lessons from the walkthrough

The first lesson was that time is the cost. Tuition made Experience+CCIM look cheapest by a wide margin, but once forgone earnings were priced, the MRED became the cheapest route to a development role.

The second lesson was that a recommendation without reversal conditions is only an opinion. The brief committed to a route, then named what could overturn it: PhD funding, a real MRED wage premium, a longer horizon, a lower discount rate, or a non-wage goal.

The third lesson was to know what a check actually checks. reconcile() can verify that the model’s calculations match expected math, but it does not prove that the assumptions are true.

## Proving Mastery: The Teach-Back Instrument

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/56f31de5-d9ab-4f61-a174-a87453f3a685_pecebod1)

### Defending the recommendation and naming reversal conditions

The funded PhD broke even at year 18.0. That came from 11 discounted earning years to repay the $104,900 investment, plus the 5-year program and 2-year academic job-market ramp.

The single input change that reversed the recommendation was the time horizon. Raising TIME_HORIZON_YEARS from 10 to 18 or more allowed the funded PhD to clear, so the model recommended it instead of Experience+CCIM.

That distinction mattered. PhD funding moved the PhD from Never to 18.0, but 18.0 still sat outside the 10-year horizon. Funding was necessary for the flip, but the longer horizon was the condition that actually changed the recommendation.

## Reflections and Lessons Learned

### Key tools and concepts

The key tools I used included Python for data modeling, numpy-financial for discounted net present value, GitHub for version control, and Linear for tracking the work.

The main concepts I learned included sensitivity analysis, break-even analysis, opportunity cost, and comparing education routes through the same financial frame. I also learned how a “free” path can become expensive when forgone earnings are counted.

The larger lesson was that a decision model should do more than produce an answer. It should make the logic clear enough to defend, challenge, and reverse when the inputs change.

### Time and challenges

This build took me approximately 60 minutes. That time covered environment setup, baseline loading, same-axes scoring, break-even logic, sensitivity analysis, decision brief generation, documentation, and teach-back validation.

The hardest part was making the teach-back script validate my reasoning instead of only checking numeric outputs. I had to explain why the recommendation held, what would reverse it, and which assumptions carried the model.

I completed this build to learn how to quantify complex career decisions with Python-based financial modeling and sensitivity analysis. Next, I want to add probabilistic forecasting or Monte Carlo simulations so the model can account for uncertainty in long-term career planning.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/56f31de5-d9ab-4f61-a174-a87453f3a685)*
