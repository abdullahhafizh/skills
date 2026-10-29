# Failure patterns

Use a name from this file only when the quote matches the symptom. Do not force a name.

## The Tutorial

Symptom: explains what a common thing is, or how an ordinary library works.
Fix: delete it. Keep decisions, trade-offs, and anti-patterns the model does not already have.

## The Dump

Symptom: `SKILL.md` holds everything, hundreds of lines past the point of use.
Fix: keep routing and decisions in `SKILL.md`. Move tables and long examples to `references/`, with a mandatory read at the step that needs them.

## The Orphan Reference

Symptom: a `references/` file exists and no step tells the agent to open it.
Fix: name the file in the step that needs it, and name the files that step must not open.

## The Checkbox Procedure

Symptom: open, find, edit, save, with no decision.
Fix: replace the sequence with what to decide before the action. Keep a sequence only when the order is one the model would get wrong.

## The Vague Warning

Symptom: "be careful", "avoid errors", "consider edge cases".
Fix: a NEVER with the act and the reason experience taught.

## The Invisible Skill

Symptom: the body is strong and the skill does not load.
Fix: the description states what, when, the words a person would use, and one case that must not load it. "Helps with tasks" does not.

## The Wrong Location

Symptom: "when to use" lives in the body.
Fix: move triggering into the description. The body is read only after the skill is chosen.

## The Over-Engineered Package

Symptom: changelog, install guide, and contributing notes shipped as if the agent needs them to do the task.
Fix: the procedure loads `SKILL.md` and the references a step names. Human-facing catalog files are not evidence of skill quality and are not required reading.

## The Freedom Mismatch

Symptom: a script for a taste task, or a slogan for a step that corrupts data.
Fix: principles where many answers are valid; exact commands where one mistake breaks the result.
