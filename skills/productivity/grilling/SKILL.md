---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Ask each round in two parts.

First, print a **round overview** in chat: one line per frontier question, in order, formatted `Q1: <title>: <one-line gist> (rec: <one-line recommendation>)`. This is the map of where the round is leading; keep it to a single line per question.

Then ask with the Ask Questions tool (`vscode_askQuestions`), one call per round, one question entry per frontier question. Do not ask questions as plain text.

- `header`: `Q<n>` plus a short title matching the overview line (max 50 chars).
- `question`: a concise prompt for the question, as required by the tool. Preserve the full question, including multiple paragraphs, in `message` without changing its substance.
- `message`: the full body, including context, trade-offs, background, and the original question wording, in markdown.
- `options`: the discrete choices (A)/(B)/(C). `label` is the choice; `description` carries its rationale or consequence.
- Set `recommended: true` on your recommended option and include the recommendation in that option's `description` (e.g. "Recommended: ..."). If no discrete choices exist, omit `options` and put the recommendation in `message`.
- Leave free-form answers enabled (`allowFreeformInput: true`) so the user can always type an answer that is not on the list.

If the Ask Questions tool is not available in this session, fall back to the numbered text format: print the round overview first, then for each question `❓ **Q<n>** - **<title>**: <body, including choices>` followed by `➡️ <your recommended answer>`, separated by `---` rules.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent to find it; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.
