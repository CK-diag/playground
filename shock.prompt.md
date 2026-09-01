Build a falsification-first Rare Shock Market Mispricing research PoC

You are the senior software architect, quantitative researcher, data engineer, and test engineer for this task.

Work autonomously inside this repository. Do not merely propose an architecture: create the working code, tests, documentation, example data, and executable analysis pipeline.

The goal is NOT to build an automated trading bot.

The goal is to build the smallest credible research platform that can falsify or support the following hypothesis:

After large negative market reactions to rare, novel, high-uncertainty and psychologically salient events, the degree of subsequent abnormal mean reversion can be predicted better by adding event psychology and causal company exposure than by using the initial price drop and ordinary market variables alone.

The key scientific question is:

Do Novelty + Uncertainty + Psychological Salience + Causal Exposure provide incremental out-of-sample predictive information about subsequent abnormal return after a shock?

The system must be designed to make it EASY to discover that the hypothesis is false.

1. Core principles

Follow these principles strictly:

Walking skeleton.
Vertical slices.
Reproducible research.
Falsification before optimization.
No trading execution.
No portfolio optimizer.
No live brokerage integration.
No automated parameter fishing.
No look-ahead information.
No survivorship bias where avoidable.
Do not fabricate historical data.
Keep raw data separate from derived data.
Cache externally retrieved data.
Every derived observation must retain provenance.
Prefer simple interpretable models before machine learning.
Compare every sophisticated model against embarrassingly simple baselines.
Tests must run offline using committed fixture data.
Real-data adapters may require internet access but the analysis pipeline itself must be reproducible from cached data.

If a requested external source cannot legally or technically be downloaded automatically, create a clean provider interface and document the manual acquisition step instead of inventing data.

2. Project location

Create the project under:

rare-shock-research/

Do not disturb unrelated files elsewhere in the repository.

Suggested structure:

rare-shock-research/
README.md
pyproject.toml
config/
data/
raw/
interim/
processed/
fixtures/
docs/
methodology.md
event_taxonomy.md
data_provenance.md
known_biases.md
architecture-decisions.md
notebooks/
src/
rare_shock/
domain/
ingestion/
features/
event_study/
models/
validation/
simulation/
reporting/
tests/

Use a modern Python package layout.

Prefer:

pandas or polars
numpy
scipy
statsmodels
scikit-learn
pyarrow/parquet
pydantic or dataclasses for domain models
typer for CLI if appropriate
pytest
matplotlib for plots

Avoid unnecessary infrastructure.

Do not introduce databases, containers, cloud infrastructure, message queues, frontends, or microservices unless clearly justified by an immediate PoC requirement.

Parquet/CSV files are sufficient initially.

3. Domain model

The primary observation is:

Event × Company

NOT merely Event.

Implement explicit models for at least:

Event

Fields should include:

event_id
event_name
event_family
event_start_timestamp
decision_timestamp
geography
description
source_references
novelty_score
uncertainty_score
salience_score
scoring_provenance
scoring_timestamp

Example event families:

pandemic
terrorism
war
geopolitical escalation
natural disaster
industrial accident
cyberattack
regulatory shock
sudden product ban
supply-chain disruption
political instability
infrastructure disruption

Do not assume these categories are exhaustive.

Company

Fields:

company_id
ticker
exchange
company_name
sector
industry
geography
market_cap_at_event where available
EventCompanyExposure

Represent causal exposure independently of psychological features.

Include:

event_id
company_id
exposure_direction
exposure_score
exposure_directness
expected_materiality
duration_uncertainty
substitutability
financial_resilience
geographic_concentration
exposure_mechanism
evidence
information_timestamp

The exposure mechanism is important.

Examples:

airspace closure
-> airline
-> direct revenue interruption

pandemic restrictions
-> hotel operator
-> occupancy collapse

war
-> manufacturer
-> loss of production facility

The model must distinguish:

media association
from
causal economic exposure
MarketObservation

For each Event × Company store:

decision_timestamp
reference_price
initial_abnormal_return
volume_information
realized_volatility
market_benchmark
sector_benchmark

Future outcome columns must be stored separately from features.

Targets:

abnormal_return_1d
abnormal_return_3d
abnormal_return_5d
abnormal_return_10d
abnormal_return_20d
abnormal_return_60d
abnormal_return_120d
max_additional_drawdown
max_recovery
optional time_to_stabilization
4. Psychological framework

Do NOT initially create one arbitrary composite score such as:

Novelty × Uncertainty × Salience

Keep dimensions separate.

Implement scoring rubrics with explicit definitions.

Each score initially uses ordinal values 0–4.

Novelty

0 = routine event with abundant comparable historical experience
1 = unusual but familiar mechanism
2 = unusual combination of familiar mechanisms
3 = very unusual situation with limited comparable history
4 = genuinely novel situation with extremely limited comparable experience

Uncertainty / ambiguity

Measure epistemic uncertainty, not market volatility.

Consider:

unknown duration
unknown severity
unknown geographic propagation
unknown government response
unknown secondary consequences
disagreement among credible forecasts
unknown probability distribution itself
Psychological salience

Consider observable or documentable characteristics such as:

human harm
vivid imagery
fear
catastrophic framing
sustained news attention
repetition
perceived personal relevance
dramatic narrative simplicity

Later implementations may add objective proxies such as:

news volume
NLP fear measures
Google Trends
headline intensity

But the initial implementation must support manually curated scores.

Exposure

Exposure is NOT psychology.

It represents the strength and mechanism of the company's actual causal economic connection to the shock.

Document all rubrics in:

docs/event_taxonomy.md

5. Critical point-in-time rule

This is non-negotiable.

For every feature used to make a hypothetical prediction at time T:

INFORMATION_TIMESTAMP <= T

The model must never use facts that became available later.

Examples of forbidden leakage:

later government support
later capital raises
later knowledge of disruption duration
later earnings impact
later analyst estimates
retrospective descriptions of company exposure

Create validation code that detects obvious timestamp violations.

Describe this in:

docs/known_biases.md

6. Initial dataset

Do NOT attempt to create a huge dataset first.

Create infrastructure supporting a manually curated gold dataset.

Initial target:

approximately 30 historical shock events
×
approximately 10–30 companies per event

Expected eventual scale:

300–900 Event × Company observations for the first meaningful PoC.

However:

Do NOT invent these observations.

For the committed repository, include a SMALL fixture dataset containing clearly marked illustrative/test observations.

Fixtures may be synthetic ONLY when explicitly labelled:

synthetic_fixture = true

Synthetic data must never be mixed into research results.

Create a schema/template that makes manual event curation easy.

Prefer CSV or Parquet.

7. Data providers

Create clean provider abstractions.

Potential sources may include:

Market prices

Implement a provider interface such as:

MarketDataProvider

Support at least one freely accessible historical-price adapter if technically practical.

Possible candidate:

yfinance for exploratory research

But document its limitations and do not make the domain layer dependent on it.

All retrieved data must be cached locally.

Event/news data

Prepare an adapter/interface for GDELT.

GDELT may later be used for:

historical event discovery
news volume
event metadata
salience proxies

Do NOT attempt to automatically infer the entire research dataset from GDELT in the first slice.

Optional future commercial sources

Document extension points for:

RavenPack
MarketPsych
Dataminr-like feeds

Do not require them.

8. Abnormal returns

Implement an event-study module.

Never evaluate using raw stock return alone.

At minimum calculate:

stock return
minus
benchmark return

Support:

market-adjusted abnormal return
sector-adjusted abnormal return where data exists

Design for later factor-model adjustment.

Document equations and assumptions.

9. Baseline models

The entire experiment depends on comparing complexity against simple baselines.

Implement models incrementally.

M0 — naive dip/reversal baseline

Features:

initial abnormal price drop only

Predict:

future abnormal return, initially 20 trading days
M1 — conventional market baseline

Features:

initial abnormal return
realized volatility
volume shock
market cap if available
sector
market regime where practical
M2 — event taxonomy

M1 plus:

event family
M3 — causal exposure

M2 plus:

exposure features
M4 — psychology

M3 plus:

novelty
uncertainty
salience
M5 — interactions

Only after M0–M4 work.

Candidate interactions:

novelty × uncertainty
uncertainty × salience
salience × initial_drop
exposure × initial_drop
uncertainty × exposure

Do not create a large feature search space.

Interactions must be explicitly declared.

10. Primary outcome

The initial primary target is:

20 trading day sector-adjusted abnormal return

If sector benchmark data is unavailable:

20 trading day market-adjusted abnormal return

Secondary horizons:

5 days
10 days
60 days

Do NOT choose the best-performing horizon after inspecting results and then pretend it was the original target.

The primary horizon must remain 20 days unless manually changed in configuration before an experiment.

11. Statistical analysis

Start with interpretable methods.

Implement:

descriptive statistics
correlation analysis
OLS / robust regression
regularized linear model where useful
confidence intervals
clustered standard errors by event where practical

Remember:

20 companies affected by COVID are NOT 20 independent pandemics.

Event-level dependence must be respected.

Report both:

number of Event × Company observations
number of independent events

Do not report naive p-values without acknowledging clustering.

12. Matched-event test

Implement support for a particularly important experiment.

Compare:

A:

large stock drop caused by a high-novelty/high-ambiguity event

against

B:

similar-sized stock drop caused by a conventional information event

Match approximately on:

initial abnormal return
sector
company size
volatility
broad market regime

Then compare subsequent abnormal return.

This directly tests:

Does a novel ambiguous shock mean-revert more than an ordinary event producing a similar initial fall?

Implement the framework even if the initial fixture data is too small for statistical conclusions.

13. Validation

DO NOT use random train/test splitting as the primary validation.

Implement:

Temporal validation

Example configuration:

train:
events before 2013

validation:
2013–2018

test:
2019 onward

Dates must be configurable.

Leave-one-event-out

No companies from the held-out event may occur in training through that event.

Leave-one-shock-family-out

Example:

train:

war
terrorism
natural_disaster
industrial_accident
regulatory

test:

pandemic

Rotate families.

This is an important test of whether the model learned a general response to novel risk rather than memorizing event categories.

14. Model comparison

Produce a table such as:

Model
Features
Train observations
Independent events
Out-of-sample R²
MAE
Directional accuracy
Mean return of high-confidence predictions

Most importantly calculate incremental value:

M1 minus M0
M2 minus M1
M3 minus M2
M4 minus M3

The critical scientific question is:

Does M4 outperform M3 out-of-sample?

If not:

the psychological hypothesis is unsupported.

The program must say so clearly.

Do not optimize results to rescue the hypothesis.

15. Trading simulation — only as final downstream evaluation

Once prediction evaluation works, implement a SIMPLE hypothetical trading simulation.

No brokerage integration.

No order placement.

No live trading.

The simulation should support:

enter at first realistically tradable price after decision timestamp
configurable bid/ask spread assumption
configurable slippage
configurable brokerage/courtage
fixed or simple risk-limited position size
20-day default holding period

Parameters should permit an Avanza-like Swedish retail-cost scenario.

Do not hard-code one current brokerage tariff.

Put transaction-cost assumptions in config.

Compare:

no strategy
simple extreme-dip strategy
psychology/exposure model strategy

Report:

total return
return per trade
hit rate
Sharpe ratio if statistically meaningful
maximum drawdown
number of trades
turnover
gross return
transaction costs
net return

Warn prominently when sample size is too small for meaningful inference.

16. Anti-overfitting rules

Implement safeguards/documentation for:

look-ahead bias
survivorship bias
hindsight event selection
multiple-testing bias
horizon selection bias
event dependence
feature leakage
data snooping
transaction-cost omission

Create:

docs/known_biases.md

Make these visible in generated reports.

17. CLI

Provide a simple CLI.

Desired commands may resemble:

rare-shock validate-data
rare-shock build-features
rare-shock run-event-study
rare-shock train-baselines
rare-shock validate-models
rare-shock run-simulation
rare-shock report

Exact names may differ if there is a better coherent design.

Also provide one command for the full reproducible pipeline, for example:

rare-shock run-poc

It should work on committed fixture data without internet access.

18. Reports

Generate machine-readable results plus a human-readable report.

Create outputs such as:

results/
model_comparison.csv
event_results.csv
simulation_results.csv
report.md
figures/

Plots should include when meaningful:

initial shock versus 20-day abnormal return
residual/reversal versus novelty
residual/reversal versus uncertainty
residual/reversal versus salience
psychology-model predictions versus outcomes
model comparison
cumulative hypothetical return

Do not make charts imply statistical certainty unsupported by sample size.

19. Tests

Write automated tests for at least:

domain model validation
point-in-time leakage checking
abnormal-return calculation
future-return calculation
event/company joins
no overlap between event-level train and test groups
leave-one-family-out split
transaction-cost calculation
deterministic fixture analysis

Tests must not rely on internet access.

20. README

README.md must explain:

Research hypothesis

State the hypothesis and null hypothesis.

Null hypothesis:

Novelty, uncertainty, psychological salience and causal exposure provide no incremental out-of-sample information about subsequent abnormal reversal beyond conventional market variables and the initial price reaction.

What this project is

A research PoC.

What this project is not

Not investment advice.
Not a production trading platform.
Not a brokerage bot.
Not proof of market inefficiency.

Running it

Exact setup and execution commands.

Reading the results

Explain M0–M5 and what outcome would falsify the hypothesis.

21. Architecture quality

Use domain concepts instead of generic utility functions.

Good:

Event
Company
Exposure
PsychologicalAssessment
MarketObservation
ResearchObservation
Experiment
ValidationSplit

Avoid unnecessary abstractions.

Separate:

data acquisition
domain data
feature engineering
statistical models
validation
simulation
reporting

External APIs must remain behind provider interfaces.

22. Development sequence

Work in this order.

Slice 1 — Walking skeleton

Build:

fixture Event
fixture Companies
fixture exposure scores
fixture market prices
abnormal-return calculation
M0 regression
single report
tests

Run it successfully.

Slice 2

Add:

novelty
uncertainty
salience
exposure
M3/M4 comparison

Run it successfully.

Slice 3

Add:

temporal splitting
leave-one-event-out
leave-one-family-out

Run successfully.

Slice 4

Add:

real market-data provider
local cache

Run successfully where network access exists.

Slice 5

Add:

GDELT/event ingestion skeleton

Do NOT attempt fully automated labeling yet.

Slice 6

Add:

simple transaction-cost-aware hypothetical simulation

Stop there.

Do NOT build production trading infrastructure.

23. Autonomous working instructions

Do not stop after writing an architecture document.

Actually create the implementation.

After each vertical slice:

run formatting/linting if configured
run tests
run the PoC
inspect failures
fix them
update README if behavior changed

Prefer working software over speculative architecture.

Do not ask me questions unless the task is genuinely impossible to proceed with safely.

Make reasonable technical decisions and record important ones in:

docs/architecture-decisions.md

If something cannot be implemented because data/API access is unavailable:

implement the interface
provide fixtures
document the limitation
continue with the rest of the walking skeleton

Never fabricate a successful external-data result.

24. Definition of Done

The first PoC is DONE when I can clone the repository and run:

[setup command]

followed by:

rare-shock run-poc

and receive a report that answers, on fixture/example data:

Does initial price drop predict 20-day reversal?
Does causal exposure improve prediction?
Do novelty + uncertainty + salience improve prediction beyond exposure?
Does the improvement survive proper event-level holdout?
Does any apparent advantage survive configurable retail transaction costs?
Is the sample sufficient to draw conclusions?

The report must explicitly support the result:

"Hypothesis not supported"

if that is what the evidence says.

Start now.

First inspect the repository, create the project structure, implement Slice 1, run it, and continue through the slices as far as can be done reliably in the current environment.