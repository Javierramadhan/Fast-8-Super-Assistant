# Skill: Skill Creator

**Trigger:** `/skill-creator`

Interview Javier about a recurring workflow, then generate a complete, ready-to-use skill folder at `.claude/skills/[skill-name]/SKILL.md`.

---

## What This Skill Does

Takes a vague workflow description → asks targeted questions → produces a `SKILL.md` that Claude can follow reliably next time without re-explaining anything.

---

## Step-by-Step

### Step 1 — Interview

Ask Javier these questions one at a time (don't dump them all at once):

1. **What's the name of this skill?** (e.g. `prototype-coder`, `screen-validator`)
2. **What triggers this skill?** (What does Javier type, paste, or say to start it?)
3. **What's the end goal?** (What does the final output look like — a file, a doc, a Figma frame, a message?)
4. **What are the inputs?** (A URL? A Figma link? A description? A screenshot?)
5. **What are the steps?** (Walk me through what you do manually right now, from start to finish.)
6. **What tools are involved?** (Figma, browser, Python, Word, etc.)
7. **What are the common mistakes or gotchas?** (Things that go wrong, edge cases, things to double-check.)
8. **What does a good output look like?** (Do you have an example, reference, or standard to hit?)

> 💡 Keep it conversational. If Javier's answers are thin, ask one follow-up before moving on.
> If Javier says "just figure it out", make a reasonable assumption and state it clearly.

---

### Step 2 — Draft the SKILL.md

Once you have enough answers, generate the skill file. Follow this structure:

```markdown
# Skill: [Name] v1

**Trigger:** `/[skill-name] [required-args]`

[One sentence: what this skill does and what it produces.]

---

## Usage

\`\`\`
/[skill-name] [arg1] [arg2]
\`\`\`

- `arg1` — what it is
- `arg2` — what it is

---

## What You Do (Step by Step)

### Step 1 — [First action]
[Clear instruction. Include exact tool names, commands, or prompts to use.]

### Step 2 — [Second action]
[Continue...]

[Add as many steps as needed. Be specific — future Claude has no memory of this conversation.]

---

## Output

[Describe exactly what the output looks like: file name, location, format.]

---

## Gotchas

- [Known issue or edge case]
- [Another one]

---

## Example

[Optional: paste a sample trigger, a sample output snippet, or a before/after.]
```

---

### Step 3 — Create the Files

1. Create the folder: `.claude/skills/[skill-name]/`
2. Write the `SKILL.md` file there
3. Update `CLAUDE.md` — move the skill from "Skills to Build (Backlog)" to "Active Skills ✅" (or add it fresh if it wasn't in the backlog)
4. Confirm to Javier: "Skill `[name]` is live. Trigger it with `/[skill-name] [args]`."

---

## Quality Bar

A good `SKILL.md`:
- Is self-contained — no context from this conversation needed to follow it
- Uses exact tool names, command flags, and file paths
- Lists gotchas explicitly — not "be careful", but *what* to be careful about
- Has a clear output definition — Javier knows exactly what to expect

A bad `SKILL.md`:
- Says "figure it out from context"
- Skips steps that seem obvious (they won't be obvious to a fresh Claude)
- Has no gotchas section
- Is vague about the output format or location

---

## Gotchas

- Skills live in `.claude/skills/` — not `skills/` at root
- Skill folder name should be kebab-case: `my-skill-name`
- The file inside is always named `SKILL.md` (uppercase)
- CLAUDE.md uses `@context/` includes — don't break those when editing it
- If Javier already has an example output, ask for it — it's the best spec
