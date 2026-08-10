---
name: setup-auditor
description: Audit your CLAUDE.md, skills, hooks and subagents against Anthropic's current published guidance for the model you are actually on, and get a delete / keep / rewrite verdict on every instruction. Use when the user says "audit my setup", "audit my CLAUDE.md", "is my CLAUDE.md out of date", "what should I delete", "check my skills against the new model", or after any new Claude model ships.
---

# Setup Auditor

Most Claude setups are archaeology. Every instruction in a CLAUDE.md was written to fix a problem some *earlier* model had, and nobody ever goes back and removes it once the model stops having that problem. The instructions stay, they cost tokens, and several of them now make output actively worse.

Anthropic's own team ran this exact clean-up: when Opus 5 shipped they deleted more than 80% of Claude Code's system prompt and it performed better without it. Their published advice is to try deleting your CLAUDE.md, your skills and your hooks every six months and see what the model does on its own.

This skill does that audit properly instead of guessing: it reads what you actually have, fetches what Anthropic actually recommends for the model you are actually on, and gives you a verdict per instruction with the source line behind it.

## The rule that makes this skill work

**Never hardcode the guidance. Always fetch it live.**

Model-specific advice changes every time a model ships. A skill with the rules baked in becomes exactly the stale artefact it was built to find. So step 2 below always goes and reads the current pages.

## Safety

- **Never delete or edit anything without explicit confirmation.** Propose, show the diff, wait.
- Back up every file you are about to change first: `cp <file> <file>.pre-audit`.
- A rule that looks redundant may exist because of a real failure the user hit. When a DELETE candidate looks deliberate, say so and ask rather than assuming it is cruft.
- Never touch files outside the ones the user named or the standard locations in step 1.

## Step 1 — Find what they actually have

Look in these locations. Report what exists before analysing anything.

```bash
# Memory / instruction files
ls -la ~/.claude/CLAUDE.md ./CLAUDE.md ./AGENTS.md ./.claude/CLAUDE.md 2>/dev/null
wc -l ~/.claude/CLAUDE.md ./CLAUDE.md 2>/dev/null

# Skills, agents, hooks
ls -d ~/.claude/skills/*/ ./.claude/skills/*/ 2>/dev/null | wc -l
ls -d ~/.claude/agents/*/ ~/.claude/agents/*.md 2>/dev/null
grep -l "hooks" ~/.claude/settings.json ./.claude/settings.json 2>/dev/null
```

Report a table: artefact, path, size in lines. Size matters — a 400-line CLAUDE.md is the strongest signal that an audit is overdue.

## Step 2 — Fetch the current guidance (never skip, never cache)

Find out which model they are on first (`/model`, or ask). Then fetch:

1. `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices` — the living cross-model reference
2. The model-specific page for their model, linked from that page. One exists per current model, for example `.../prompt-engineering/prompting-claude-opus-5`
3. `https://claude.com/blog/best-practices-for-prompt-engineering` — the general craft post

If a fetch fails, say so and mark the affected checks NOT RUN. Never substitute remembered guidance for a failed fetch.

## Step 3 — Run the checks

Two passes. The delete pass first, because it is where the wins are.

### DELETE candidates

Search their files for each pattern. Quote the line you found and the guidance line that condemns it.

| # | Look for | Why it goes |
| --- | --- | --- |
| 1 | Explicit verification steps: "verify before done", "include a final verification step", "use a subagent to verify", "prove it works" | Anthropic: remove these on Opus 5, they cause over-verification and waste tokens with no quality gain. Applies to harness scaffolding that adds a separate verify step too |
| 2 | Re-check instructions: "double-check your answer", "re-verify before responding", "challenge your own work" | The model already self-corrects. These compound with its own behaviour and add cost without improving results |
| 3 | Any rule telling the model not to think or not to reason | Increases internal-tag leakage into visible output. Remove it |
| 4 | Review prompts saying "only report high-severity issues" or "be conservative" | Taken literally, so it reports less. Ask for everything and filter in a second pass instead |
| 5 | Vision workarounds tuned for an older model | Re-validate. They are often no longer needed |
| 6 | Effort defaults carried over from a previous model | Re-run an effort sweep. `low` and `medium` now hold quality at a fraction of the cost |
| 7 | XML-tag scaffolding and heavy role-play treated as mandatory structure | Once recommended, now optional. Keep where it genuinely disambiguates mixed content, drop where it is ceremony |

**False-positive guard on check 4.** "Only report work you can point to evidence for" is a TRUTHFULNESS rule, not a severity filter. Same for any "only claim what you verified" line. Those are KEEP. Check 4 fires only on instructions that narrow what gets reported *by severity or confidence*, never on ones that forbid fabrication. Getting this wrong makes the setup worse, not better.

### ADD candidates

Newer models are more verbose and more eager. Check whether they have each of these, and flag the gap.

| # | Missing instruction | Why it is needed now |
| --- | --- | --- |
| 1 | An explicit conciseness instruction | Default responses run longer than prior models. Effort controls thinking, not output length, so length has to be prompted for |
| 2 | Written-deliverable length calibration | Files written to disk come out longer and padded unless calibrated |
| 3 | Narration cadence for agentic work | The model announces what it is about to do. Describe the cadence you want, with positive examples rather than prohibitions |
| 4 | A task-scope constraint | It expands scope and adds unrequested steps. Constrain it explicitly for narrow tasks |
| 5 | A subagent delegation cap | It delegates readily, which multiplies cost on small tasks |

## Step 4 — Output

Give them this, in this order.

1. **One-line verdict.** `Your CLAUDE.md is 412 lines. 9 instructions should go, 4 are missing. Estimated saving: N lines.`
2. **The delete table.** One row per hit: the line, its file and line number, which check caught it, and the source sentence from Anthropic's docs. No hit means no row — never pad the table to look thorough.
3. **The add table.** One row per gap, with the exact paste-able text to add.
4. **The six-month experiment.** Tell them the stronger move plainly: rename the file, run a week without it, and add back only what you actually miss. `mv CLAUDE.md CLAUDE.md.parked`
5. **What you did not check.** Any failed fetch, any file you could not read, any check that did not run. State it as NOT RUN.

Then stop. Do not apply anything until they say which rows to action.

## The paste-able version (no Claude Code needed)

Not everyone runs Claude Code. Give this to anyone who lives in the chat box — they paste their CLAUDE.md or custom instructions in underneath it.

```text
You are auditing my Claude instructions to find what is now out of date.

First, read Anthropic's current guidance:
- platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- the model-specific prompting page linked from it, for the model I am using
- claude.com/blog/best-practices-for-prompt-engineering

Then go through my instructions below, line by line, and give me a table with one row per instruction:

| My instruction | Verdict | Why | Anthropic's line |

Verdict is DELETE, KEEP or REWRITE.

Be specific about why. For every DELETE and REWRITE, quote the sentence from Anthropic's own guidance that justifies it. If you cannot find one, mark it KEEP and say the guidance does not cover it. Do not guess and do not invent a source.

Pay particular attention to instructions that tell you to verify your work, double-check your answers, be conservative, or not to think. Those were written for older models and now make output worse.

Then tell me the four or five instructions I am missing that the newest models actually need, and give me the exact wording to add.

Report everything you find. I will decide what to cut.

My instructions:
[paste here]
```

## Failure modes to avoid

- **Reporting a clean audit you did not run.** If a fetch failed or a file was unreadable, say NOT RUN. Never a green tick by default.
- **Inventing a source line.** Every DELETE must quote real text from a page you actually fetched this run.
- **Padding the table.** Nine real findings beat twenty with eleven maybes.
- **Deleting a rule that earned its place.** Scar tissue is not always cruft. Flag it and ask.
- **Caching the guidance.** Fetch every run. A stale auditor is the problem, not the fix.
