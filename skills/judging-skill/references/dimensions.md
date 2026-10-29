# Dimensions

Score bands for one skill. Quote a line before choosing a band.

## D1 — Knowledge delta (20)

Does the skill add knowledge the model does not already have?

| Score | Criteria |
|------:|----------|
| 0–5 | Basics the model knows: what X is, how to write ordinary code, standard-library tours. Any of the red flags below caps the dimension at 5. |
| 6–10 | Some expert material, diluted by obvious paragraphs. |
| 11–15 | Mostly expert, little redundancy. |
| 16–20 | Every paragraph earns its tokens. |

Red flags: "what is [basic concept]", tutorials for ordinary operations, common-library walkthroughs, slogans ("write clean code"), definitions of ordinary terms.

Green flags: a decision the model would get wrong ("when X fails, try Y because Z"), a trade-off from experience, a real edge case, "NEVER do X because [non-obvious reason]".

For each section ask whether the model already knows it. Explaining to the model is redundant. A rule the model must apply is expert.

Ratio of expert : activation : redundant. Above 70% expert is the target. Under 40% expert is a bad skill. Count paragraphs, do not guess.

## D2 — Mindset and procedures (15)

Thinking patterns plus procedures the model would not invent. Generic open/edit/save steps do not count.

| Score | Criteria |
|------:|----------|
| 0–3 | Only procedures the model already knows. |
| 4–7 | Domain steps, no thinking framework. |
| 8–11 | Both, with gaps. |
| 12–15 | Both, and the order is one the model would get wrong. |

Valuable procedures name an order that is easy to miss ("validate before packing"). Valuable thinking is "before doing X, ask Y". A good skill has both when the task needs both.

## D3 — Anti-patterns (15)

Half of expert knowledge is what not to do. The reason must be one that experience teaches.

| Score | Criteria |
|------:|----------|
| 0–3 | No anti-patterns. |
| 4–7 | Vague warnings ("avoid errors", "consider edge cases"). |
| 8–11 | A specific NEVER list with some reasons. |
| 12–15 | Specific acts plus why, the kind an expert learned by failing. |

The test: an expert says "I learned that the hard way", or they say "everyone knows that". The second one does not score in the top band.

## D4 — Specification (15)

Focus on the description. It is the only text used to decide whether the skill loads.

| Score | Criteria |
|------:|----------|
| 0–5 | No frontmatter, or the frontmatter is invalid. |
| 6–10 | Frontmatter exists; the description is vague. |
| 11–13 | Valid frontmatter; what is present; when is weak. |
| 14–15 | What, when, trigger words, and a case that must not load this skill. |

`name`: lowercase, alphanumeric and hyphens, at most 64 characters, equal to the directory name.

The description must state what it does, when to use it, and searchable words (file names, user phrases, verbs). "Helps with documents" fails. A description that never says when not to use it stays at or below 13 when a neighbor skill could also match.

## D5 — Progressive disclosure (15)

Layer 1 is `name` and `description` (always visible). Layer 2 is `SKILL.md` (after triggering; under 500 lines, under 300 is the aim). Layer 3 is `references/`, `scripts/`, `assets/`, loaded only when a step says to.

| Score | Criteria |
|------:|----------|
| 0–5 | One dump, over 500 lines, no layering. |
| 6–10 | Reference files exist; nothing says when to read them. |
| 11–13 | The workflow says when a file is mandatory. |
| 14–15 | Mandatory reads, plus an explicit "do not load" for files that do not apply. |

A skill with no references and under 100 lines is scored on whether it is complete without them. Listing files at the bottom with no trigger is a 6–10.

## D6 — Freedom (15)

Match constraint to the cost of a mistake. High cost means low freedom. Low cost means high freedom.

| Score | Criteria |
|------:|----------|
| 0–5 | Rigid steps for a taste task, or vague steps for a fragile one. |
| 6–10 | Mixed, with a clear mismatch. |
| 11–13 | Right level on most of the file. |
| 14–15 | Right level throughout. |

Creative work gets principles, not one script. A format that breaks on one wrong byte gets an exact command and no invitation to improvise. Judgment tasks (review, scoring) get an ordered checklist and room on the wording.

## D7 — Pattern (10)

| Pattern | Shape | Use when |
|---------|--------|----------|
| Mindset | Short, taste, strong NEVER, high freedom | Creative work |
| Navigation | Short router into other files | Several distinct scenarios |
| Philosophy | Stance, then execution | Craft where originality matters |
| Process | Phases and checkpoints, medium freedom | Multi-step work |
| Tool | Decision tables, low freedom | Exact operations on one format |

| Score | Criteria |
|------:|----------|
| 0–3 | No pattern. |
| 4–6 | A pattern, with large breaks. |
| 7–8 | A clear pattern, small breaks. |
| 9–10 | The pattern matches the task. |

One skill should follow one pattern. A philosophy essay glued to a tool manual is 4–6.

## D8 — Usability (15)

An agent can finish the task from the file.

| Score | Criteria |
|------:|----------|
| 0–5 | Contradictory, incomplete, or unusable. |
| 6–10 | Usable, with a gap on a common case. |
| 11–13 | Common cases are clear. |
| 14–15 | Common cases, edges, and a fallback. |

A branch needs a condition, not "use the appropriate tool". A fragile step needs the failure and the next attempt. An example that is only a slogan does not count as a worked example.

## Worked band

Object, the whole file:

```markdown
---
name: helper
description: Helps with tasks
---
# helper
Be careful with edge cases.
```

The score is a point inside the band, not the top of it.

| Dimension | Quote | Band | Score | Why this band, not the next one |
|---|---|---|---:|---|
| D1 | `Be careful with edge cases.` | 0–5 | 3 | The only body line is a slogan. No decision the model would get wrong. |
| D2 | `Be careful with edge cases.` | 0–3 | 1 | No question to ask before acting, and no order the model would get wrong. |
| D3 | `Be careful with edge cases.` | 4–7 | 5 | A warning with no act and no reason. The 8–11 band needs a specific NEVER. |
| D4 | `description: Helps with tasks` | 6–10 | 8 | Frontmatter exists. No when, no keyword, no case that must not load it, so not 14–15. |
| D5 | `Be careful with edge cases.` | 6–10 | 7 | That line names no file to open, so the 11–13 band, which needs a mandatory read, is closed. |
| D6 | `Be careful with edge cases.` | 6–10 | 7 | The only instruction is a slogan. A wrong reading still ships, and the line gives no band to obey. |
| D7 | `# helper` | 0–3 | 1 | The only heading has no phases, no router, and no decision table under it. |
| D8 | `Be careful with edge cases.` | 6–10 | 8 | The line is one path. It does not say what to do when `description` is missing. |

If the description line is absent, D4 stays in 0–5. Do not skip D1–D3 and D5–D8, and do not call them perfect. Quote another line or score that dimension 0.
