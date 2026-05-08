---
name: agentic-patterns-thinking-doc
description: >
  Generates a complete thinking document for any agentic design pattern or
  agent architecture in the exact style of the "Agentic Design Patterns:
  The Evolutionary Thinking Framework" session document. Use this skill
  whenever a student wants to deeply understand an agentic pattern or
  architecture — not just its mechanics but the full strategic thinking
  behind it: pattern selection, tool design, retrieval pipelines, planning
  vs reactive loops, reflection variants, hallucination control, assumption
  diagnostics, and agent orchestration moments. Trigger this skill when
  the user says things like "help me understand [pattern] the way we did
  agentic patterns", "build a thinking doc for [pattern/architecture]",
  "apply the 13 frameworks to [pattern]", "walk me through [pattern] like
  the session", or any request to deeply understand an agent system from
  first principles using the evolutionary thinking approach. This skill
  works for ANY agentic pattern or architecture — Tool Use, RAG, Agentic
  RAG, Planning, ReAct, Reflexion, Self-Refine, Plan-and-Execute, Tree of
  Thoughts, multi-agent orchestration, supervisor patterns, hierarchical
  agents, code-generation agents, deep-research agents, and beyond.
---

# Agentic Pattern Thinking Doc Generator

## FIRST THING TO DO BEFORE ANYTHING ELSE

Read the reference file at: `references/agentic-patterns-thinking-doc.md`

This is the complete Session document — the gold standard for style, depth,
structure, and quality. Every document this skill generates must match it.
Specifically internalize before starting:
- The 13 thinking frameworks (exact names, numbers, and core insights)
- The 8 AI coding agent moment formats (structure, prompt style, reality check format)
- The narrative voice — plain language first, intuition before jargon, always
- The "REALITY CHECK — If you ignore this concept" format used after every major concept
- The 7-question agent interrogation template at the end
- The comparison table structure (pattern composition / tool design / grounding / assumptions)

Do NOT start collecting inputs from the student until you have read this file.

---

## What this skill does

This skill generates a complete, deeply structured thinking document for any
agentic pattern or architecture. The output mirrors the style, depth, and
pedagogical approach of the "Agentic Design Patterns: The Evolutionary
Thinking Framework" document — which is the gold standard for how this
program teaches agent systems.

The document is NOT a tutorial. It is NOT a how-to guide. It contains NO code.
It is a thinking system — built around the 13 thinking frameworks and 8 agent
moments from the agentic patterns session — applied to a new pattern so the
student can transfer their thinking, not just learn new facts.

The document is generated section by section with a pause after each one.
The student reads, absorbs, and types "continue" before the next section appears.

---

## STEP 0 — Before generating anything, collect three inputs

Ask the student these three questions in a single message. Wait for all three
answers before proceeding.

```
Before I build your thinking document, I need three things:

1. Which agentic pattern or architecture do you want to explore?
   (any pattern is fine — ReAct, Agentic RAG, Plan-and-Execute,
   Reflexion, Tree of Thoughts, supervisor multi-agent, hierarchical
   agents, code-generation agents, deep-research agents, or anything
   else)

2. What industry or domain do you work in or want to apply this to?
   (e.g. fintech, edtech, healthcare, e-commerce, SaaS, logistics,
   legal — this anchors every business example in the document to
   your reality)

3. Have you read the Agentic Design Patterns Thinking Document
   covering the four core patterns (Tool Use, RAG, Planning,
   Reflection)?
   (yes / no / partially — this changes how I connect concepts back
   to what you already know)
```

**If the student gives a non-agentic concept** (pure prompt engineering with
no tools/retrieval/planning, or a pure ML algorithm):
Redirect gently — "this skill is scoped to agentic patterns and architectures
where an LLM decides actions and tools/retrieval/coordination are involved.
[name] is [pure prompting / supervised learning / etc.] — want to pick an
agentic pattern instead, or shall I explain the boundary?"

**Store the three answers internally.** Every section that follows uses them:
- Pattern name → drives all technical content
- Domain → replaces every generic example with a domain-specific one
- Foundational doc familiarity → if yes, explicitly connect back throughout;
  if no, add more foundational context in each section

---

## STEP 1 — The Human Story

Generate 3–4 paragraphs telling the story of where this pattern came from.

**This is not a Wikipedia summary. This is a narrative.**

Cover:
- What real-world problem someone was actually trying to solve
- What pattern or approach existed before this one and why it was failing
- The specific moment, paper, or person where the pattern emerged
- Why the pattern was the inevitable answer to that specific frustration

**Quality bar:** After reading this section, the student should feel like
this pattern was the only logical response to a specific engineering problem —
not like a researcher invented it in the abstract.

End every section with this exact pause:

```
---
Take a moment to read this section.
When you're ready to continue, type: continue
---
```

---

## STEP 2 — The Intuition Build

Generate a plain-language explanation of the pattern's core idea using an
example from the student's domain (from their answer to question 2).

**Rules for this section:**
- No technical jargon for the first 3 paragraphs minimum
- Start from something the student has experienced in their industry
- Show how the natural human behavior in that situation IS the pattern
- The student should recognize their own intuition before they see the name

**Examples by domain (adapt, don't copy):**
- Fintech: a loan officer who consults the credit policy doc before deciding
- Edtech: a tutor who plans the session before starting
- Healthcare: a triage nurse who consults protocols and double-checks meds
- E-commerce: a support agent who looks up order history before responding
- SaaS: a sales rep who plans the call sequence before reaching out
- Legal: a paralegal who pulls relevant cases before drafting

After the intuition is established in plain language, introduce the
pattern's name and formal identity — but only after the concept exists.

End with the standard pause block.

---

## STEP 3 — The Pattern Anatomy

**This is where the architectural structure is introduced — but intuitively first.**

Generate this section in three parts:

**Part A — Plain language anatomy**
What does this pattern actually look like as a system? What pieces does it
have? How do they connect? Explain this structure in one paragraph without
any code or diagrams.

**Part B — The anatomy table**
Always produce this exact table:

| What the pattern is | What it can handle | What it cannot handle | What you're betting on |
|---------------------|-------------------|----------------------|----------------------|
| [fill in]           | [fill in]         | [fill in]            | [fill in]            |

**Part C — The four-pattern comparison**
Explicitly answer: how does this pattern relate to the four core patterns
(Tool Use, RAG, Planning, Reflection)? Is it a specific instance of one?
A composition of multiple? An extension or variant? What does that
composition mean for when you would choose it?

This comparison must appear here AND in the final comparison section.
Don't save it all for the end.

End with the standard pause block.

---

## STEP 4 — The Tool / Retrieval / Coordination Choices

Generate this section in four parts:

**Part A — Plain language explanation**
What are the key design choices in this pattern? Tool granularity, retrieval
strategy, plan format, reflection depth? Explain the way the agentic doc
explained tool design — through a real situation where the wrong choice
causes a specific, painful business failure. Use the student's domain.

**Part B — Why these specific choices**
Why did this pattern emerge with these specific design choices? What
practical property made them the right choice for this pattern's purpose?
Connect to the "RAG won because retrieval grounds answers in evidence"
style of explanation from the agentic doc.

**Part C — Thinking Framework #2 or #3 applied (whichever fits)**
Produce a labeled box in this exact format:

```
THINKING FRAMEWORK #[2 or 3] APPLIED TO [PATTERN NAME]:
[Every tool is a hypothesis / The retrieval pipeline is a business decision]

[2-3 paragraphs showing how this plays out differently than in the basic
four patterns. What kinds of design choices does this pattern force? What
business situations call for a different approach? What would you tell
the framework to change about the default and why?]
```

**Part D — Reality Check**
Produce a labeled box in this exact format:

```
REALITY CHECK
If you ignore this concept:
- [specific failure scenario 1 in the student's domain]
- [specific failure scenario 2 in the student's domain]

[one sentence summary of the consequence]
```

End with the standard pause block.

---

## STEP 5 — The Coordination / Control Flow

Generate this section covering how the pattern actually orchestrates work.

**Rules:**
- Explain the control flow in plain language before any pseudocode
- Explicitly compare to the simplest agent loop (ReAct / Tool Use loop):
  - Is it the same loop with extra steps? (e.g. Reflexion — yes, with critique)
  - Is it a variation? (e.g. Plan-and-Execute — adds a planning phase)
  - Is it completely different? (e.g. Tree of Thoughts — branching search;
    multi-agent supervisor — coordination across agents instead of one loop)
- The "completely different" cases are the most important — they force the
  student to re-examine what they assumed was universal

**For patterns that extend the basic ReAct loop:**
Apply Thinking Framework #5 explicitly — label it, show how the variant
choice (no-plan / static plan / replanning / hierarchical) plays out for
this specific pattern.

**For patterns that do NOT use a single agent loop:**
This is a major insight moment. Produce a callout:

```
THIS IS WHERE THE REAL LEARNING IS:
[Pattern name] has no single agent loop. There is no "decide one action,
execute, repeat" cycle. Instead: [explain the actual mechanism — branching,
multi-agent handoff, hierarchical decomposition, etc.].

This means Thinking Framework #5 (planning is the universal coordinator)
has an important qualifier: it's universal for *single-agent* systems.
[Pattern name] is [explain the category — multi-agent / search-based /
hierarchical].

What this teaches you about agent thinking: [the conceptual insight]
```

**Always include:** the failure modes specific to this pattern's
coordination — what goes wrong in practice that simple ReAct loops don't
produce (e.g. handoff loss in multi-agent, search explosion in ToT,
plan-execution skew in Plan-and-Execute).

End with the standard pause block.

---

## STEP 6 — All 13 Thinking Frameworks Applied

This is the centerpiece section. Go through all 13 frameworks one by one.

For each framework, produce:

```
THINKING FRAMEWORK #[N]: [Framework name]

[Core insight from the agentic doc — one sentence]

Applied to [pattern name]:
[2-3 paragraphs showing exactly how this framework plays out for this
specific pattern. Use the student's domain for any examples.]

Compared to the four core patterns:
[ ] Identical — works exactly the same way
[ ] Similar — same principle, different execution
[ ] Fundamentally different — and here is why that matters:
[explanation of the difference and what it teaches]
```

**The 13 frameworks to cover:**

1. Pattern selection is the highest-leverage skill
2. Every tool is a hypothesis — know its limitations before you give it to the agent
3. The retrieval pipeline is a business decision, not a technical one
4. The universal agent architecture: Tool Use, RAG, Planning, Reflection
5. Planning is the universal coordinator, but its variants matter enormously
6. The composition vs complexity tradeoff defines senior agent engineers
7. Hallucination is the silent killer
8. How you ground answers matters as much as that you ground them
9. Reflection is universal — but what kind of correctness do you need?
10. Report business metrics, not just technical ones
11. The best tools come from domain workflows, not technical generalizations
12. Violated assumptions give you confidently wrong agents
13. The pipeline is universal, but the gotchas at each stage are where projects die

**Pacing note:** This is the longest section. After frameworks 1–4, insert
a mid-section pause:

```
---
That covers the first four frameworks. Take a moment.
When you're ready for frameworks 5–13, type: continue
---
```

Then generate frameworks 5–13, then the standard end-of-section pause.

---

## STEP 7 — Agent Moments (minimum 3, maximum 5)

Generate at least 3 "AI Coding Agent Moment" sections in the exact format
used in the agentic patterns document.

**Format for each agent moment:**

```
AI CODING AGENT MOMENT #[N]: [Decision name]

Why the framework cannot do this alone:
[1 paragraph explaining the specific business or domain context the
framework is missing — not "it doesn't know your data" but the specific
strategic knowledge required]

What an expert tells the agent:
[Multi-line prompt template the student can paste directly.
Must be specific, not generic. Should include:
- The business context
- The specific asymmetry or constraint the framework doesn't know
- What to compare or produce
- What format the output should take]

REALITY CHECK
If you ignore this concept:
- [specific failure scenario]
- [specific failure scenario]

[one line summary]
```

**The agent moments must be specific to this pattern.** Do not recycle
agent moments from the agentic patterns document. Each pattern has its own
critical decision points. Examples:

- Agentic RAG: query reformulation strategy and when to stop retrieving
- Plan-and-Execute: replanning trigger conditions and step granularity
- Reflexion: critic prompt design and the "approved" threshold
- Tree of Thoughts: branching factor and pruning heuristics
- Supervisor multi-agent: handoff protocol and shared state design
- Hierarchical agents: scope boundaries between parent and child agents
- Code generation agents: test-driven loops and verification depth

End with the standard pause block.

---

## STEP 8 — Real-World Framing Examples (3, domain-specific)

Generate 3 detailed business scenarios from the student's domain where
this pattern is the right choice.

**Format for each scenario:**

```
Scenario [N]: [Scenario name in the student's domain]

The business question:
[What the stakeholder is actually asking]

The naive framing most people would use:
[What a junior engineer would build and why it's wrong or suboptimal]

The strategic framing:
[Why this pattern specifically — not just "it works for agents"
but the specific property of this pattern that matches this problem]

What success looks like in business terms:
[Not task success rate or tool accuracy — the actual business outcome.
Tickets resolved without escalation, time saved, errors prevented.]

The framing trap to avoid:
[The specific way this scenario tempts you into the wrong framing,
and the signal that you've fallen into it]
```

End with the standard pause block.

---

## STEP 9 — When It Breaks

Generate the specific, non-obvious failure modes of this pattern.

**Rules:**
- No generic statements ("agents can hallucinate" — every pattern section says this)
- Only failure modes specific to this pattern's structure
- Each failure mode must include: what it looks like when it's happening,
  why it's hard to detect, and what the consequence is in production

**Always cover:**
- What system characteristics cause this specific pattern to fail silently
- What assumption violations are unique to this pattern (not shared with
  basic Tool Use / RAG)
- What the output looks like when the pattern is failing but metrics look fine
- One real-world case study style example in the student's domain

**Produce a "failure signature" table:**

| Failure mode | What triggers it | What it looks like | Why it's invisible | Production consequence |
|--------------|-----------------|-------------------|-------------------|----------------------|
| [fill]       | [fill]          | [fill]            | [fill]            | [fill]               |

End with the standard pause block.

---

## STEP 10 — The Comparison Anchor

This section does not exist as a single explicit section in the agentic
patterns document. It exists here because the student already has the four
core patterns as their foundation, and this section makes the transfer of
thinking explicit.

Generate three parts:

**Part A — The comparison table**

| Dimension | Core Four Patterns | [Pattern Name] | What the difference teaches |
|-----------|-------------------|----------------|----------------------------|
| Anatomy | Tool Use / RAG / Planning / Reflection | [pattern's anatomy] | [insight] |
| Control flow | Single agent loop | [pattern's flow] | [insight] |
| Tool design | Per-pattern, individually | [pattern's tool design] | [insight] |
| Coordination | Within one agent | [pattern's coordination] | [insight] |
| Failure modes | Per-pattern, isolated | [pattern's failure modes] | [insight] |
| Reflection role | Optional add-on | [pattern's reflection role] | [insight] |
| When it breaks | Each pattern's own gaps | [pattern's specific breaks] | [insight] |
| Agent moment | Per-pattern decisions | [key moment] | [insight] |

**Part B — What is identical**
2 paragraphs on what works exactly the same way as the four core patterns.
The point: when you encounter a new pattern, you should immediately
recognize the parts you already understand.

**Part C — What is fundamentally different and why it matters**
2 paragraphs on the deepest conceptual difference. Not a surface difference
(different control flow) but a structural difference (different category of
coordination, different correctness model, different failure surface).
End with: "This difference matters because in production, it means..."

End with the standard pause block.

---

## STEP 11 — The 7-Question Interrogation (completed for this pattern)

The agentic patterns document ends with a 7-question template the student
can use for any future agent pattern. Complete it now for this pattern.

```
THE 7-QUESTION AGENT INTERROGATION: [Pattern Name]

1. HUMAN PROBLEM: What real-world action or answer does this pattern provide?
   [answer]

2. PATTERN MIX: Which of the four core patterns does it use? Why those?
   [answer]

3. TOOL DESIGN: What tools does it have? Reversibility? Approval gates?
   [answer + the question to ask yourself]

4. GROUNDING: How does it know things? How is each claim verifiable?
   [answer]

5. ASSUMPTIONS: What does it assume about the world? How do you check?
   [answer + the diagnostic to run]

6. FAILURE MODES: When does it loop, hallucinate, or stop early?
   [answer]

7. PRODUCTION GAPS: What breaks between demo and production?
   (cost, latency, hallucination, escalation, audit trails)
   [answer — specific to this pattern]
```

After generating this, add:

```
Keep this completed interrogation. The next time you encounter a paper,
blog post, or colleague mentioning this pattern, you now have a one-page
answer to every question a senior engineer will ask you about it.

When you're ready to build the thinking doc for your next pattern,
run this skill again.
---
```

---

## CRITICAL STYLE RULES — enforce throughout every section

These are non-negotiable. Every section must follow them.

**1. Intuition before jargon — always**
Every technical term is introduced with a plain-language concept first.
The student understands the idea before they see the name. No exceptions.

**2. Domain specificity — always**
Every business example uses the student's domain from question 2.
"A company" is not acceptable. "A fintech startup running SME loan support
in India" is acceptable. Generic examples are a failure of this skill.

**3. Explicit framework labeling — always**
Every thinking framework section is labeled with its number and name in
the exact format: "THINKING FRAMEWORK #[N]: [Name]"
Frameworks are never embedded invisibly in prose.

**4. Pasteable agent prompts — always**
Agent moment prompts must be multi-line, specific, and pasteable directly
into Claude. "Tell the framework to add reflection" is not a prompt.
A prompt is a paragraph of specific instructions with context included.

**5. Reality checks — always**
Every major concept gets a reality check box. Not every section — every
concept. If you've introduced a concept that someone could ignore and
later regret, it gets a reality check.

**6. Comparison to the four core patterns — always**
The comparison appears in multiple sections, not just Section 10. Every
time a framework is applied or a coordination flow is explained, there is
an explicit note on how it compares to the four core patterns (Tool Use,
RAG, Planning, Reflection).

**7. No code — ever**
This document contains zero code. Not even pseudocode unless it is
genuinely the clearest way to express an idea. The agentic patterns
document has minimal code in the chapter excerpts but the *thinking doc*
itself has none. This document has none.

**8. Depth over speed**
This is not a summary. It is not a cheat sheet. It is a full thinking
document. Each section should be as long as it needs to be to reach
the quality bar. The agentic patterns document is comparable in depth
to the regression document. This document should match.

---

## Quality bars by section — how to know when a section is ready

| Section | Quality bar |
|---------|-------------|
| Human story | Student feels the pattern was inevitable, not arbitrary |
| Intuition build | Non-technical colleague could follow first 3 paragraphs |
| Pattern anatomy | Student can explain the bet they're making before building |
| Tool/retrieval/coordination | Student can justify a non-default design to a VP of Engineering |
| Control flow | Student can diagnose a coordination failure without help |
| 13 frameworks | Every framework has a "same/similar/different vs core four" judgment |
| Agent moments | Prompts are pasteable — no editing required before running |
| Framing examples | Examples are specific enough to be wrong in a specific way |
| When it breaks | Failure modes are specific to this pattern, not generic agent advice |
| Comparison anchor | Student can answer "why not just use plain ReAct?" in 30 seconds |
| 7-question interrogation | Completed answers are specific, not generic — a senior engineer finds no gap |

---

## Handling edge cases

**If the student asks for a very simple pattern (e.g. plain Tool Use, plain RAG):**
These patterns are often dismissed as "the basics." Resist this.
The thinking frameworks still apply fully. The control flow section becomes
an opportunity to teach what it means when there IS no advance plan or
critic — which is one of the most important conceptual insights in the
program.

**If the student asks for a complex pattern (e.g. multi-agent supervisor,
hierarchical agents, deep-research agents):**
Don't compress. Don't summarize. These patterns have more to say in
every section, not less. The agent moments section is especially rich
for complex patterns — more decisions, more places where human judgment
is irreplaceable.

**If the student hasn't read the foundational doc (answered "no" to question 3):**
Add a brief foundational paragraph at the start of sections 3, 4, and 5
that establishes the four-pattern baseline before the comparison. Don't
assume they have the anchor. Build it for them.

**If the student asks follow-up questions mid-section:**
Answer them in context, then return to the pause. Don't skip the pause
because a question was asked. The pause exists to let the concept settle.

**If the student types something other than "continue":**
If they ask a question — answer it. If they push back on a concept —
engage with it. The pause-and-continue flow is the default rhythm, but
learning conversations take detours and that's correct behavior.
