---
name: foreverlm-ai-cost-review
description: Analyze what the learner spends on AI in ForeverLM from the app's own usage ledger, find the tasks and models that dominate the bill, and propose concrete Settings changes that lower it, such as a cheaper catalogued model for background tasks or a lower effort or speed tier. Use when the user asks to analyze their AI costs, what they are spending on AI or on models, why their balance drains, how to reduce AI costs, or whether to switch to cheaper models. Apply a change only after the learner approves it.
---

# ForeverLM AI cost review

The learner's spend lives in ForeverLM's AI usage ledger, and the model every task runs on is a setting in Settings > AI Models. This skill reads both through the plugin, proposes cheaper settings with an estimated saving, and applies only what the learner approves. Never read `knowledge.db` or any other app file directly; the plugin is the route, and a plugin failure is reported, not worked around.

## Read before proposing

1. Call `get_mcp_status`. Both readers and the writer are answered by the learner's Mac; if the Mac is not reachable, say so and stop.
2. Call `get_ai_usage_report`. Default to the last 30 days; use `days`, or `from` and `to` (local `YYYY-MM-DD`, `to` inclusive), when the learner names a period. Read `totals`, `by_task` (sorted by spend, each with the models it ran, `per_call` averages and `current_route`), `by_model` and `by_day`.
3. Call `get_ai_task_settings`. It is Settings > AI Models as data: `default_model`, `background_text`, `background_multimodal`, every Advanced `tasks` row with the route it resolves to, and the `catalogue` with each model's `price` per million input, output, cached-input and cache-write tokens, `supports_vision`, `supported_efforts`, `route_can_carry_speed` and `selectable`.

## What the numbers mean

- `cost_usd` is what the provider billed when it reported an amount, else the list-price estimate stamped when the call was recorded. `unpriced_calls` were excluded from every total: a total with unpriced calls beside it is a floor, and you say so.
- `per_call.cost_usd` divides the priced total by the priced calls only. `per_call.input_tokens`, `per_call.output_tokens` and `per_call.cache_read_tokens` are averages over all of the task's calls.
- `current_route` is what the task would run on now. It may differ from the models in the task's `models` list, which are what actually ran during the period; base the saving on the current route's price, and mention when a recent settings change already moved the task.
- A task with `configurable: false` (for example `chat_title` or `read_aloud`) follows the feature that made it and has no setting of its own.
- `price_known: false` means the pricing table has no row for that model. Never fill the gap with a guess; write "unknown" and leave that row out of the arithmetic.

## Find what dominates

Rank `by_task` by `cost_usd` and name the tasks that make up most of the spend. For each, note its call count, average tokens in and out, cache read share, and the route it runs on. Then look at `by_model` for a frontier model that several small background tasks share: that is usually where the saving is.

## Propose cheaper settings

For every dominating task that is `configurable`, consider, in this order:

1. **A cheaper catalogued model of the same maker or class for background work.** Only a model from the `catalogue` with `selectable: true`, on the same or a comparable class (a Flash, Haiku, mini, or an open-weight model on OpenRouter for a metadata or classification task). A task with `requires_vision: true`, and the `background_multimodal` default, take only a model whose `supports_vision` is `true`.
2. **A lower effort where the model offers one.** `supported_efforts` lists what the current model accepts; a lower effort spends fewer output tokens, and the saving is estimated from output tokens only.
3. **A slower or cheaper speed where the route carries one.** `route_can_carry_speed: true` means the OpenRouter route lists tiers; `flex` is about half the standard price where offered and `fast` costs more.
4. **Moving a chatty background task off a frontier model.** If a task with many calls and small prompts follows the Default Model or a frontier override, pin it to a cheaper model as an Advanced override, or change the Background default when most background tasks would benefit.

Never propose a model the catalogue does not list, a provider that is not in `accepts.providers`, or a retired model. Leave chat's Default Model alone unless the learner asks about chat itself; a cheaper chat model changes the quality of every conversation.

## Estimate the saving honestly

For a model change, take the task's `per_call.input_tokens` and `per_call.output_tokens`, multiply by the price difference per million tokens between the current route and the proposed model, and multiply by the period's `calls`. Use `cache_read_tokens` with the cached-input price when both models publish one. Write the result as "about $X over the last N days at the same usage" and show the arithmetic. When either price is unknown, or the task's calls are mostly unpriced, say plainly that the saving cannot be estimated for that row.

## Present, then ask

Present the proposal as a table before touching anything:

| Task | Calls | Spend | Runs on | Proposed | Estimated saving |
|---|---|---|---|---|---|

Under it, note the trade-off of each row in one line (a smaller model reads fewer nuances; a lower effort reasons less). Then ask which rows to apply. Do not apply anything the learner has not approved, and do not treat a general "reduce my costs" as approval of a specific model change.

## Apply what was approved

For each approved row call `set_ai_task_settings` once:

- A per-task change: `target: "task"`, `task`, `provider`, `model`, and `effort` or `speed` when the row changes those.
- A Background default: `target: "background_text"` or `"background_multimodal"` with `provider` and `model`.
- The Default Model: `target: "default_model"` with `provider` and `model`, only when the learner asked for it.
- Undoing an override: `target: "task"`, `task`, `reset: true`.

The reply carries the resulting settings and `changed`; read them back and confirm each applied row against what was approved. A refused selection comes back as an error naming the rule that refused it; report it rather than retrying with a different model on your own.

## Report

Close with the period, the total spend and how many calls were unpriced, the rows applied, the rows the learner declined, and the estimated saving of what was applied. Suggest running the same report after a week to compare.
