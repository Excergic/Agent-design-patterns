# Tool Use — The Evolutionary Thinking Document

**A complete guide from the foundational pattern to senior-engineer thinking**  
**Applied to B2B AI SaaS**

---

## Part 1: The Human Story — Where Tool Use Came From

For the first two years after ChatGPT, the entire world experienced LLMs the same way: **as a brilliant, articulate friend trapped behind glass.**

You could ask it anything. It would answer almost anything.  
**But it could not do anything.**

---

A founder of a B2B SaaS company would sit in front of GPT-4 and ask:

> “Which of my Pro-tier customers had declining usage last month?”

And the LLM — confidently, fluently, with perfect grammar — would **make up an answer**.  
Or it would say, “I don’t have access to your data.”

Neither response was useful.

The founder had **real customers**, **real usage logs**, and **real Stripe events**.  
The LLM had **words**.

There was a wall between them.

---

### The Wall

This wall was the most important problem in applied AI in 2022.

Every team building on top of LLMs hit it within their first week:

- **Customer support teams** → “The LLM can write the perfect refund email, but it cannot actually look up the order or issue the refund.”
- **Sales teams** → “The LLM can summarize a CRM entry beautifully, but only if I copy-paste it in — it can’t read Salesforce on its own.”
- **Internal ops teams** → “The LLM can draft a great Slack post about the deploy status, but it cannot check whether the deploy actually succeeded.”

**Every single B2B SaaS use case** ran into the same wall:  
**The LLM had reasoning but no hands.**

---

### The Breakthrough

The breakthrough came from two key developments:

1. The **ReAct** paper (Yao et al., 2022)
2. OpenAI’s **function calling** release shortly after

The insight was almost embarrassingly simple in hindsight:

> **Instead of asking the LLM to answer the question, ask it to decide which function should be called to get the answer.**

The LLM doesn’t need to know your customer’s MRR.  
It just needs to know that a function called `get_customer_mrr(customer_id)` exists — and when to call it.

**The function does the actual work.**  
**The LLM does the reasoning** — which function, with which arguments, in which order.

---

### The Wall Came Down

Suddenly the same LLM that could only *talk* about your B2B SaaS could now:

- Query your database
- Post to Slack
- Create Jira tickets
- Update HubSpot

**Tool Use** was the pattern that turned a chatbot into an agent.

And it remains **the foundational pattern** that every other agentic pattern builds on top of.


## Part 2: The Intuition Build

Imagine you’ve just hired a brilliant new product manager at your B2B SaaS company.  

**Day one.**  

She’s sharp — Stanford MBA, ex-McKinsey, talks about your market better than your founders do.  

You walk her into a meeting room and ask:  

> “Which of our enterprise customers are at risk of churning this quarter?”

She freezes.

Not because she doesn’t know how to *think* about churn — she knows the frameworks better than anyone.  

She freezes because she’s been at the company for **four hours**. She doesn’t have a Looker login. She can’t query the data warehouse. She doesn’t know which Slack channels the CSMs post in. She has no idea where the renewal forecast spreadsheet lives.

Her brain is full of **relevant thinking** and completely empty of **relevant facts** about your company.

---

### What Does She Do?

She doesn’t make up an answer.  

She turns to you and says:

> “Can you give me access to the usage dashboard?  
> And introduce me to whoever owns the renewals tracker?  
> And add me to the customer-success Slack channel?”

She is **asking for tools**.  

Specifically, she is asking for **the smallest set of tools** that will let her actually do the job you hired her to do.

She doesn’t ask for root access to production.  
She doesn’t ask to be added to every system.  

She asks for the **precise tools** that let her reasoning connect to your reality.

---

### Now Replace the PM with GPT-5

The model is just as smart.  
It also has **zero access** to your company’s reality.

The difference?  
**GPT-5 can’t ask for tools — you have to give them.**

---

### The Entire Game of Building Agents

The entire game of building an agent for your B2B SaaS company is exactly this:

> Figuring out **the smallest, sharpest set of tools** that lets the LLM’s reasoning connect to your business reality.

| Too Few Tools                  | Too Many Tools                          | **Just Right**                          |
|--------------------------------|-----------------------------------------|-----------------------------------------|
| Agent is articulate but useless | Agent gets confused, hallucinates actions, or picks wrong tool | Agent acts like your best PM — at scale, 3am, in 12 languages |
| Like the PM with no logins     | Risk of irreversible actions            | Precise leverage                        |

---

### The Core Intuition

**Tool Use** is not a technical trick.  

It is the pattern of giving an intelligent reasoner — one that has no hands — **a precisely chosen set of hands** that are useful in *your* specific business.

The formal name is **Tool Use**.  
The technical primitives are tool schemas, tool calls, tool execution, and the agent loop.  

But the underlying idea is the one you just felt in your gut:

> An intelligent reasoner that doesn’t have hands needs the right hands to become truly useful.


## Part 3: The Pattern Anatomy

### Part A — Plain Language Anatomy

A Tool Use system has **four moving parts**. Once you see them, you’ll recognize them in every agent you ever encounter:

1. **The LLM (The Reasoner)**  
   The brain that decides what to do next.

2. **The Tool Registry**  
   The list of functions the LLM is allowed to call. Each tool has a **name**, a clear **description**, and a **parameter schema** (what arguments it needs).

3. **The Executor**  
   The code that actually runs the chosen function when the LLM decides to call it.

4. **The Message History**  
   The running conversation log — what the user asked, what the LLM decided, what the tools returned, and what the LLM said next.

---

### How the Loop Works

The LLM looks at the user’s request + current message history → decides to call a tool (or answers if done) → the executor runs the tool → the result is appended to the history → the LLM thinks again.

**That loop — Look → Decide → Execute → Append → Repeat — is the entire pattern.**  
Everything else is just configuration.

---

### Part B — The Anatomy Table

| What the Pattern Is | What It Can Handle | What It Cannot Handle | What You’re Betting On |
|---------------------|--------------------|-----------------------|------------------------|
| An LLM that decides which external function to call, runs it, sees the result, and decides what to do next — **in a loop**, until it has an answer or hits a limit. | Tasks where the work needed is a **function call**: querying a database, calling an API, sending a notification, computing something deterministic, taking an action in a SaaS system. | • Tasks requiring complex multi-step planning over many dependencies (needs **Planning**)<br>• Tasks needing private knowledge not in a queryable system (needs **RAG**)<br>• Tasks where wrong outputs are catastrophic (needs **Reflection**) | That the LLM correctly understands each tool’s purpose from its description, picks the right tool at the right time, passes correct arguments, and knows when to stop. |

---

### Part C — How Tool Use Relates to the Four Core Patterns

**Tool Use is the foundational pattern.**  
It is not just *one* of the four — it is the **substrate** that the other three sit on top of.

| Pattern     | Relationship to Tool Use                                                                 | What It Really Is |
|-------------|-------------------------------------------------------------------------------------------|-------------------|
| **RAG**     | RAG is Tool Use where the tool is a retriever (`search_docs(query)`)                      | Specialized Tool Use + extra concerns around chunking & grounding |
| **Planning**| Planning adds a planner on top of Tool Use when multiple tools must be used in order     | Tool Use + deliberate sequencing |
| **Reflection** | Reflection is Tool Use where one of the tools is a critic/verifier LLM                   | Tool Use + self-critique capability |
| **Tool Use**| The core loop itself                                                                      | The foundation |

---

> In B2B AI SaaS, **every agent** you’ll ever build for a customer is a Tool Use system at its core — possibly wearing the costume of one of the other three patterns.

When you choose to build a “RAG agent”, “planning agent”, or “reflection agent”, you’re not picking between four different things.  
You’re deciding **how much structure to add** on top of the same foundational Tool Use loop.

**The quality of your tool design sets the ceiling for everything else.**

---

# Tool Use — The Evolutionary Thinking Document

**A complete guide from the foundational pattern to senior-engineer thinking**  
**Applied to B2B AI SaaS**

---

## Part 4: The Tool Design Choices

### Part A — Plain Language Explanation

The **single highest-leverage decision** in any Tool Use system is **what tools you give the agent** and **how you define them**.

Not the model.  
Not the framework.  
Not the prompt.  

**The toolset.**

---

Picture this:

You’re building an agent for a mid-market HRIS company called **PeoplePulse**. Their support team handles 4,000 tickets a month — “I can’t log in,” “Why was my employee charged twice?,” “How do I add a new department?,” “Cancel our subscription.”

You’re tasked with building an agent that resolves the first two categories without human intervention.

You sit down to define the tools. Two instincts pull at you:

- **Instinct 1 (Simple)**: Give the agent one powerful `query_database(sql_string)` tool and let the LLM write the SQL.  
- **Instinct 2 (Safe)**: Give it twenty narrow, named tools (`get_user_by_email`, `get_user_login_history`, `list_failed_payments_for_employee`, etc.).

**Both feel reasonable. Both are wrong — in different, expensive ways.**

---

**The single-tool disaster**:  
The LLM tries to help a customer who can’t log in and generates:  
`DELETE FROM users WHERE last_login IS NULL`  
→ 3,000 user accounts deleted.  
→ $400K liability cap is about to be tested.

**The twenty-tool disaster**:  
Tool descriptions balloon to 6,000 tokens. Tool selection accuracy drops to 71%. The agent confidently calls the wrong lookup function and gives incorrect answers.

**Tool design is not a generic engineering decision.**  
**It is a business decision dressed up as engineering.**

---

### Part B — Why These Design Dimensions Matter

Tool Use emerged with very specific design knobs because each one corresponds to **real business risk** that teams discovered in 2023–2024 production deployments:

- **Granularity** (one tool vs many) → LLMs fail with overly generic tools  
- **Read vs Write separation** → Wrong writes are orders of magnitude more expensive  
- **Approval gates** → Some actions are too irreversible for “the LLM decided”  
- **Schema strictness** → Free-form parameters lead to hallucinated fields  

These are not theoretical. They are **scar tissue** from a thousand B2B SaaS production incidents.

---

### Part C — Thinking Framework #2 Applied

```markdown
THINKING FRAMEWORK #2 APPLIED TO TOOL USE:

Every tool is a hypothesis — know its limitations before you give it to the agent.

In B2B AI SaaS, every tool you give the agent is a hypothesis with 
a contractual and reputational price tag attached.

When you give the agent `issue_refund(amount)` without an approval gate, 
you are betting that the LLM will correctly interpret refund eligibility.

When you give it a generic `update_customer_record(fields)` tool, 
you are betting it will only change what the user asked for — not seven other fields it “noticed could probably be updated.”

Each tool’s existence is a bet.

And in B2B SaaS, your bets are visible: they appear in audit logs, compliance reports, 
and ARR-impacting incidents that escalate from your customer’s CTO to your CEO.

```

```
B2B SaaS default bias: Narrow, named, read-mostly tools with explicit approval gates on writes.

Never accept the template “give the agent these 20 tools and let it figure it out.”

For every tool, force yourself to answer:

(a) Worst-case if called with wrong arguments?
(b) Is the worst-case reversible automatically or does it need human cleanup?
(c) Does the customer’s contract actually allow this action without a human?

If you can’t answer those three questions, you don’t have a tool design — you have a liability surface.
```

### Part D — Reality Check

REALITY CHECK

If you ignore this concept:

• You ship an agent with a generic run_sql tool.  
  Three weeks later it runs a query that locks the production payments table 
  for 18 minutes during peak hours.  
  Your customer’s CTO is in your Slack at midnight asking why your “AI feature” 
  took down their billing.

• You ship twenty similar narrow tools.  
  Tool selection accuracy = 78%.  
  The agent confidently mixes up billing and user data.  
  Customers don’t notice for weeks.  
  On the renewal call the question isn’t “can you fix it?” — 
  it’s “how do we know what else was wrong?”


```markdown

The wrong toolset doesn’t make your agent fail obviously.
It makes your agent fail with full confidence and clean log lines saying “tool call succeeded.”
In B2B SaaS, the customer usually finds out before you do.

```

## Part 5: The Coordination / Control Flow

**This is where Tool Use reveals its true nature.**

It has the **simplest control flow** of any agentic pattern — and that simplicity is both its greatest strength and its most underestimated trap.

---

### The Tool Use Loop (Plain Language)

1. User sends a message.  
2. The LLM looks at the message + available tools + message history.  
3. The LLM does one of two things:  
   - **(a)** Decides to call a tool → Executor runs it → Result is appended to history → Go back to step 2.  
   - **(b)** Decides it has enough information → Produces a final answer for the user.

**No upfront plan.**  
**No critic.**  
**No decomposition.**  

Just: *Look → Decide → Act → Observe → Repeat.*

This is the canonical **ReAct loop** (Reasoning + Acting). Every major framework — LangChain, LlamaIndex, Anthropic tool use, OpenAI function calling — implements some version of it as the default.

---

### How It Compares to Other Patterns

**Tool Use is the baseline.**  
It is the trunk. Everything else is a branch.

| Pattern      | How It Modifies the Tool Use Loop                  | When It Adds Value |
|--------------|----------------------------------------------------|--------------------|
| **Tool Use** | Pure reactive loop (ReAct)                         | Simple, scoped tasks |
| **RAG**      | Inserts a retrieval step before reasoning          | Knowledge-heavy queries |
| **Planning** | Adds an upfront decomposition / roadmap step       | Multi-step dependencies |
| **Reflection**| Adds a critique / verification step before final answer | High-stakes accuracy |

---

### The Reactive Nature — The Insight Most Engineers Miss

The loop is **purely reactive**.

The LLM never says:  
> “To solve this, I will call Tool A, then Tool B, then Tool C.”

It only ever says:  
> “Given everything I see **right now**, the next best action is X.”

After X finishes, it looks at the new state and decides again.

**This works beautifully for short, obvious tasks** — e.g., “What’s the MRR for Acme Corp?” (one tool call → done).

**It breaks down on tasks with hidden dependencies** — e.g., “For our top 10 churning customers, summarize their support tickets and flag any mentioning competitors.” The agent can lose track of the original goal, run out of context, or skip key analysis.

---

### Thinking Framework #5 Applied to Tool Use

```markdown
THINKING FRAMEWORK #5 APPLIED TO TOOL USE:

Planning is the universal coordinator, but its variants matter enormously.

Tool Use sits at the "no plan" end of the spectrum. 
This is a deliberate choice — not a missing feature.

Question for every B2B SaaS use case: 
Is "no plan" the right variant for this customer’s task?


Examples from PeoplePulse (HRIS):

“I can’t log in” → Pure Tool Use (no plan) is perfect. Two tool calls, clear path.
“Why was my employee charged twice?” → No-plan starts failing due to hidden dependencies. Better to use a static plan (clarify run → pull data → identify duplicates).

Recommended Variants in B2B SaaS:

No plan (pure Tool Use): Scoped, predictable tasks
Static plan upfront: Tasks with stable but hidden ordering
Replanning on failure: Tasks with flaky tools or messy data

Hierarchical planning is almost always overkill for single-workflow B2B agents.

Failure Modes Specific to Tool Use
Because the loop is reactive and has no built-in plan, three characteristic failures appear:

Infinite Looping
Agent keeps calling the same tool with the same arguments.

Premature Stopping
Agent calls one tool, gets a partial result that looks complete, and answers the user anyway.

Tool Confusion
Calls get_user_account when it needed get_billing_account because descriptions overlap.

Diagnostic Trick (Production Gold):
Print the full message history of a failing run. Read it as if you were the LLM. If you would have made the same decision, the problem is almost never the model — it’s in tool descriptions, granularity, or stopping conditions.

```

Part 6: All 13 Thinking Frameworks Applied to Tool Use
This is the centerpiece. Each framework, applied specifically to Tool Use, with an explicit judgment of how it relates to the foundational pattern map. We'll do frameworks 1–4 first, then pause.

THINKING FRAMEWORK #1: Pattern selection is the highest-leverage skill

Core insight: The most important decision in any agent project happens before
you write a prompt — which patterns does this problem actually need?

Applied to Tool Use:
For Tool Use specifically, the pattern selection question becomes inverted.
Instead of asking "should I use Tool Use?", you ask "is Tool Use *enough*?"
Because Tool Use is the foundational pattern, you almost always need it. The
real question is whether to add Planning, RAG, or Reflection on top. In B2B
AI SaaS, the temptation is to start with all four because the customer's use
case "feels complex" — but most B2B SaaS workflows are actually two-to-four
tool calls with no hidden dependencies, and pure Tool Use ships in a week
while a four-pattern composition takes a quarter. The senior move is to ship
pure Tool Use to your design partner first, watch where it specifically fails,
then add exactly the pattern that addresses the specific failure. Don't add
Reflection because "errors might be costly" — add it because you saw the
agent confidently miscategorize a churn signal in week 2 of the pilot.

Compared to the four core patterns:
[X] Identical — works exactly the same way

Pattern selection thinking is identical for Tool Use as for any other pattern.
The framework is: start with the minimum viable architecture (which for most
B2B SaaS workflows is pure Tool Use), and add structural complexity only when
you've seen the specific failure that justifies it. This is the same discipline
the session document urged — and it applies most strictly to Tool Use because
Tool Use is the easiest pattern to over-engineer.

THINKING FRAMEWORK #2: Every tool is a hypothesis — know its limitations
before you give it to the agent

Core insight: Each tool you give an agent is a bet that the LLM will use it
correctly; tool granularity is a tradeoff between capability and safety.

Applied to Tool Use:
This framework is *most* important for Tool Use because Tool Use *is* tools.
There's no other layer to compensate for bad tool design — no planner that
will spot the wrong tool choice, no reflector that will catch the wrong
argument. In pure Tool Use, the toolset is the agent. For B2B AI SaaS, this
means tool design isn't a sub-task of building the agent — it *is* building
the agent. When you sit down with PeoplePulse to scope the support agent, the
two-week tool design conversation is the project. The prompt engineering, the
LLM selection, the framework choice — those are an afternoon each. The tool
schemas, the granularity decisions, the approval gates, the reversibility
analysis — that's the work. If you find yourself spending 80% of project time
on prompts and 20% on tool design in a B2B SaaS engagement, the project is
upside-down and will fail in production.

Compared to the four core patterns:
[X] Fundamentally different — and here is why that matters:

In RAG, the analogous framework is about retrieval pipeline design — and you
have grounding checks (citations, source attribution) as a backstop. In
Planning, you have the plan structure as a sanity check. In Reflection, the
critic catches some tool-use mistakes. In *pure* Tool Use, there is no
backstop. The tool design *is* the system's correctness model. This is why
Framework #2 carries more weight in Tool Use than in any other pattern, and
why senior B2B SaaS engineers spend disproportionate time on tool schemas,
naming, and granularity. A 2% tool-selection error rate is fine on a personal
assistant. On a B2B SaaS contract, it's a renewal risk.

THINKING FRAMEWORK #3: The retrieval pipeline is a business decision,
not a technical one

Core insight: Different retrieval choices produce different business outcomes
from the same documents.

Applied to Tool Use:
Pure Tool Use doesn't have a retrieval pipeline — but it has the analog: the
**tool registry** as a business decision. Which tools to include, which to
omit, which to gate, which to expose to which customer tier. In B2B AI SaaS,
the tool registry is contractually significant. A customer on your "Standard"
tier might get an agent with read-only tools. A customer on "Enterprise" might
get write tools with their internal approval workflow integration. A customer
in a regulated vertical (FinServ, HealthTech) might get a tool registry that
explicitly excludes anything that could touch PHI or PII without their MFA
gate. This isn't engineering work — it's product work. Your customer success
team and your security team need to be in the room when the tool registry is
defined per tier.

Compared to the four core patterns:
[X] Similar — same principle, different execution

The principle is identical: the configuration of how the agent connects to
information/action is a business decision. In RAG it's the chunking and
retrieval pipeline. In Tool Use it's the tool registry composition. In both
cases, the worst failure mode is engineers making the choice based on
technical convenience instead of business risk. The execution differs because
tools are usually fewer than chunks (10–30 vs millions) and the decisions are
discrete rather than statistical. But the framework is the same: business
people in the room, business risks documented per choice.

THINKING FRAMEWORK #4: The universal agent architecture — Tool Use, RAG,
Planning, Reflection

Core insight: Every agent ever built combines some subset of the same four
primitives.

Applied to Tool Use:
Tool Use is the *foundation* of this framework — the primitive on which the
other three sit. A "RAG agent" is Tool Use where one tool is a retriever. A
"Planning agent" is Tool Use with a planner deciding the next action. A
"Reflection agent" is Tool Use plus a critic LLM that re-reads the output.
For B2B AI SaaS strategy, this matters because when a sales prospect says
"we want a planning agent," they almost always mean "we want Tool Use that
handles a multi-step workflow." The label is wrong; the underlying need is
Tool Use with a small planning layer. Translating customer language ("AI
agent," "AI assistant," "Copilot," "Workflow agent") into the four-pattern
map is a senior-engineer skill that prevents you from over-architecting
based on customer terminology.

Compared to the four core patterns:
[X] Fundamentally different — and here is why that matters:

Tool Use is the foundation, not a peer. The other three patterns are extensions
of the Tool Use loop, not alternatives to it. When you build any non-Tool-Use
agent, somewhere inside it there is a Tool Use loop doing the actual work.
This means: master Tool Use first, deeply, and the other three patterns
become "Tool Use with extra structure" rather than three separate things to
learn. Engineers who try to learn all four patterns simultaneously usually
end up with a shallow understanding of all four. Engineers who deeply master
Tool Use first absorb the other three in a fraction of the time.

THINKING FRAMEWORK #5: Planning is the universal coordinator, but its
variants matter enormously

Core insight: No-plan, static plan, replanning, hierarchical — the variant
choice matters as much as having a plan at all.

Applied to Tool Use:
Pure Tool Use is the "no plan" variant — and that's a deliberate choice with
specific consequences. For B2B AI SaaS, the no-plan variant works for
predictable, scoped customer workflows: log-in unlock, MRR lookup, invoice
download, single-record updates. The moment a customer's workflow has
"and then if X, do Y, else do Z" — even just one branch — pure Tool Use
starts producing inconsistent results across runs. Senior engineers learn
to spot the inflection point: when your eval set shows the agent succeeding
on simple tickets and failing on tickets that require any conditional logic,
that's the signal to add a static plan. Not a hierarchical plan. Not full
replanning. Just a static plan that names the steps. Most B2B SaaS workflows
don't need more.

Compared to the four core patterns:
[X] Similar — same principle, different execution

The principle that variants matter is identical across patterns. In Reflection,
the variant choice is self vs cross-model vs tool-grounded. In RAG, it's
dense vs sparse vs hybrid. In Tool Use's planning dimension, it's no-plan vs
static vs replanning. The execution differs because Tool Use's "no plan"
default is more often correct than other patterns' defaults — because most
B2B SaaS individual workflows are short. But the discipline of explicitly
choosing the variant rather than defaulting is the same.

THINKING FRAMEWORK #6: The composition vs complexity tradeoff defines
senior agent engineers

Core insight: When a single-pattern agent fails, you can either add complexity
to the existing agent or compose multiple agents/patterns.

Applied to Tool Use:
For Tool Use systems in B2B AI SaaS, this framework shows up as the
"toolset bloat" decision. You have a Tool Use agent with 8 tools handling
support tickets. The customer wants it to also handle billing inquiries.
You have two options: add 6 billing tools to the same agent (now 14 tools,
prompt accuracy starts to degrade), or compose two specialized agents —
a router that decides "support ticket vs billing inquiry" and dispatches
to a support-Tool-Use-agent or a billing-Tool-Use-agent. The composition
move is almost always right when the tool sets don't naturally overlap.
The complexity move is right when the tools share a domain and removing
them would create artificial seams. The senior judgment is recognizing
which is which — and the tell is whether your support agent and billing
tools share customers, share entities, share auth contexts. If yes,
keep them in one agent. If no, compose.

Compared to the four core patterns:
[X] Identical — works exactly the same way

The composition vs complexity tradeoff applies the same way to Tool Use as
to any other pattern. The cue is identical: when in doubt, lean toward
composing simpler agents rather than building one giant agent. The trap is
identical: junior engineers add tools to an existing agent because it feels
faster than building a new one, until the agent has 25 tools and 60% tool-
selection accuracy.

THINKING FRAMEWORK #7: Hallucination is the silent killer

Core insight: Confident wrong answers are indistinguishable from correct
ones without verification.

Applied to Tool Use:
Tool Use has a specific hallucination signature that's worth memorizing
because it differs from the RAG hallucination pattern. In RAG, the LLM
hallucinates content that wasn't in the retrieved chunks. In Tool Use,
the LLM hallucinates **tool call arguments** and **tool call interpretations**.
A pure Tool Use agent for PeoplePulse might call get_user(email="actual_email")
correctly, get back {"status": "active", "last_login": "2024-11-03"}, and then
tell the user "your account is active and you logged in last week" — when
"last week" is a hallucinated interpretation of a date the LLM didn't actually
parse correctly. The tool call was right. The tool result was right. The
*interpretation* of the result is wrong. This is harder to catch than RAG
hallucination because there's no missing source to point at — the source is
right there in the message history. The fix is reflection that re-reads tool
results and verifies the LLM's interpretation matches the actual data.

Compared to the four core patterns:
[X] Fundamentally different — and here is why that matters:

In RAG, hallucination is "the LLM made up a fact that wasn't retrieved." In
Tool Use, hallucination is "the LLM correctly retrieved a fact and then
mis-stated it." This matters because the verification strategy is different.
RAG hallucination is caught by checking claims against retrieved chunks.
Tool Use hallucination is caught by checking claims against tool **results
in the message history**. The audit pattern is different. In B2B SaaS this
matters because the audit trail looks clean — the tool call succeeded, the
result is logged — but the customer-facing answer is wrong. Diagnosing this
requires reading the LLM's interpretation, not just verifying the tool ran.

THINKING FRAMEWORK #8: How you ground answers matters as much as that
you ground them

Core insight: The grounding strategy — sources, tool-grounded numbers,
verified citations — matters as much as the existence of grounding.

Applied to Tool Use:
For pure Tool Use, grounding means the agent's answer should be traceable
back to specific tool calls and specific tool results. In B2B AI SaaS, this
becomes a customer-facing feature. PeoplePulse's CSM asks the agent "is
Acme Corp at risk of churning?" and the agent should answer with "Yes,
because [tool call: get_usage_metrics returned 60% drop] and [tool call:
get_support_tickets returned 4 escalations in the last month]." Not "yes
because they seem at risk." The tool-grounded answer is auditable; the
ungrounded answer is the start of an incident. This matters more in B2B
than B2C because B2B customers will literally ask, in writing, "show me
the data this conclusion is based on" — and the answer needs to be in the
agent's response, not in your engineers' Sentry logs.

Compared to the four core patterns:
[X] Similar — same principle, different execution

The principle is identical: grounding is mandatory and the grounding strategy
is a first-class design decision. The execution differs because Tool Use
grounds answers in tool results (structured data, exact values) rather than
retrieved text chunks. This is actually a *cleaner* grounding model — exact
numbers and structured fields are easier to cite than paragraph excerpts.
B2B SaaS Tool Use systems should lean into this: every customer-facing claim
should be expressible as "this came from tool X's result Y."

THINKING FRAMEWORK #9: Reflection is universal — but what kind of correctness
do you need?

Core insight: Self / cross-model / tool-grounded / multi-aspect — match the
variant to the stakes.

Applied to Tool Use:
Pure Tool Use ships without reflection. That's the definition. The question
this framework forces in B2B AI SaaS is: when does the customer's risk
profile mean pure Tool Use is irresponsible to ship? The answer is rarely
"never" — most B2B SaaS Tool Use agents ship without reflection in their
first version because reflection doubles latency and most v1 use cases are
read-mostly. But the threshold is specific: the moment the agent gets
write tools with customer-visible consequences (sending emails to end-users,
updating customer-facing records, triggering workflows in the customer's
other systems), pure Tool Use becomes a liability and reflection becomes
non-optional. The variant for B2B SaaS write-tool reflection is almost
always tool-grounded reflection: the critic re-reads the tool calls and
asks "given the user's original request, do these tool calls match the
intent and only the intent?" Multi-aspect reflection is overkill for most
B2B SaaS Tool Use until you're in a regulated vertical.

Compared to the four core patterns:
[X] Fundamentally different — and here is why that matters:

In RAG, reflection checks generation against retrieved evidence. In Planning,
reflection checks step outputs against plan requirements. In Tool Use,
reflection has a unique form: it checks the *match between user intent and
tool call sequence*. The question isn't "is the answer factually correct"
(the tools are the source of truth) — it's "did the agent do what the user
actually asked, or did it do something adjacent that the LLM thought was
better?" This intent-fidelity check is the form of reflection most specific
to Tool Use, and it's the one most missed by engineers who think reflection
is just "have another LLM grade the output."

THINKING FRAMEWORK #10: Report business metrics, not just technical ones

Core insight: Stakeholders make decisions on rupee impact, not tool call
accuracy.

Applied to Tool Use:
For B2B AI SaaS Tool Use systems, the stakeholder is your customer's
decision-maker — usually a VP or C-level — and the metric they renew on is
not tool-call accuracy. It's "how many of my support tickets did this resolve
without my team touching them?" or "how much CSM time did this save in
QBR prep?" The technical metrics — tool selection accuracy, average tool
calls per task, tool execution success rate — are for your team's internal
debugging. The business metrics for customer reporting are: deflection rate
(% of tickets resolved without human escalation), time-to-resolution
improvement vs baseline, and dollar-cost saved per customer per month net
of any error remediation. The bridge metric — the one both sides care about —
is escalation rate, because high escalation rate means the agent isn't
deflecting work and the customer is paying you to make their support team's
job harder.

Compared to the four core patterns:
[X] Identical — works exactly the same way

This framework is identical across all four patterns. The discipline is the
same: technical metrics for internal use, business metrics for stakeholder
reporting, and a clear translation between them. The trap is identical:
engineers reporting tool selection accuracy to a VP who doesn't care about
tool selection. The fix is identical: every customer dashboard shows the
business metrics; the technical metrics live in your engineering Notion.

THINKING FRAMEWORK #11: The best tools come from domain workflows, not
technical generalizations

Core insight: Tools should mirror the verbs the human team uses today.

Applied to Tool Use:
This is the single most important framework for Tool Use system design,
and the one B2B AI SaaS engineers under-invest in most. The right way to
design a tool registry for PeoplePulse's support agent is to sit with three
of their support reps for two days and watch them work. Note every action
they take: "I look up the user by their work email," "I check their last
login event," "I check whether their company's SSO is misconfigured," "I
issue a password reset and ping their IT admin." Those verbs become your
tools. `get_user_by_work_email`. `get_last_login_event`. `check_sso_health`.
`issue_password_reset_and_notify_admin`. The tools mirror the workflow,
which means the LLM (which is good at language) can map customer intent
to tool selection naturally. The wrong way is to design tools by looking
at PeoplePulse's database schema or API surface — that gives you tools
that match their data model, not their workflow, and the LLM has to
translate between two different ontologies on every call.

Compared to the four core patterns:
[X] Identical — works exactly the same way

Domain workflow grounding applies identically to all four patterns. In RAG,
chunking and retrieval should match how domain experts find information.
In Planning, plan structure should match how experts decompose tasks. In
Reflection, the critic's checklist should match what an expert reviewer
would check. The principle is the same: ground the agent in what the human
team actually does, not in technical abstractions. The execution differs
only in which artifact gets the workflow grounding (tools, chunks, plans,
checklists).

THINKING FRAMEWORK #12: Violated assumptions give you confidently wrong agents

Core insight: Agents with violated assumptions can complete tasks "successfully"
while doing exactly the wrong thing.

Applied to Tool Use:
Tool Use carries a specific set of assumptions worth naming because they're
distinct from RAG/Planning/Reflection assumptions. (1) The LLM correctly
understands each tool's description. (2) The LLM correctly maps user intent
to tool selection. (3) Tool descriptions are unambiguous enough that two
similar tools don't get confused. (4) Tool results are interpretable by the
LLM in their raw form. (5) The user's intent is fully captured in their
initial message, so the agent doesn't need to ask follow-ups. Every one of
these assumptions gets violated regularly in B2B AI SaaS production. The
diagnostic for each is concrete: (1) tool selection accuracy on a labeled
eval set; (2) intent-tool mapping audit on real conversations; (3) tool-pair
confusion matrix for similar-named tools; (4) check whether tool results
contain encoded data (timestamps, IDs, JSON-in-strings) the LLM might
mis-read; (5) count of conversations where the agent acted before clarifying
intent and got it wrong.

Compared to the four core patterns:
[X] Fundamentally different — and here is why that matters:

Each pattern has its own assumption set. RAG assumes retrieval surfaces the
truth. Planning assumes plans stay valid through execution. Reflection
assumes the critic's blind spots differ from the generator's. Tool Use
assumes the tool descriptions encode meaning unambiguously. In B2B SaaS,
this matters because the *audit* differs per pattern. You don't audit a
Tool Use system the same way you audit a RAG system. The Tool Use audit is
focused on tool selection and tool-result interpretation. Confusing the
audits — running RAG-style hallucination checks on a Tool Use system —
catches some but misses the Tool-Use-specific failure modes.

THINKING FRAMEWORK #13: The pipeline is universal, but the gotchas at each
stage are where projects die

Core insight: Step limits, audit logs, escalation paths — the boring infra
is where reliability lives.

Applied to Tool Use:
Tool Use has its own gotcha list at each pipeline stage, and B2B AI SaaS
projects die on these specifically. Problem definition stage: "we need a
Tool Use agent" when 60% of the workflow is deterministic if-this-then-that
that doesn't need an LLM. Pattern selection: defaulting to pure Tool Use
when the task has dependencies that need a planning step. Tool design:
the gotcha list is the longest — generic tools, ambiguous descriptions,
no approval gates on writes, no schema validation, missing reversibility
analysis. Knowledge engineering doesn't apply (no retrieval). Prompt design:
no max-iterations limit (the agent loops forever), no stopping condition
("when do I stop calling tools and answer?"), no refusal policy for
out-of-scope requests. Evaluation: only testing happy-path tool calls,
missing the adversarial cases where users provide malformed inputs or
ambiguous intent. Deployment: no per-tool-call audit log (so when something
goes wrong you can't reconstruct the decision sequence), no escalation
path for low-confidence cases, no kill switch for individual tools when
they start failing.

Compared to the four core patterns:
[X] Identical — works exactly the same way

The pipeline is universal and so is the principle that each stage has its
own gotchas. The gotcha *content* differs per pattern, but the discipline of
running the pipeline-stage-by-stage is identical. For B2B AI SaaS Tool Use,
the gotchas that kill projects most often are at the prompt design stage
(missing limits) and deployment stage (missing audit infra) — because
these stages get treated as engineering hygiene rather than as core
correctness infrastructure. They are core correctness infrastructure.


Part 7: AI Coding Agent Moments for Tool Use
These are the specific decision points in any B2B AI SaaS Tool Use project where the framework or coding agent cannot make the call alone. They need your domain knowledge, your customer's risk profile, and your strategic judgment. Each prompt is pasteable directly into Claude or any coding agent — no editing required.

AI CODING AGENT MOMENT #1: Tool granularity decision for the customer's risk profile

Why the framework cannot do this alone:
A coding agent doesn't know your customer's contractual liability cap, doesn't
know which actions are reversible by their internal automation vs require
manual cleanup, and doesn't know which tools their security team would refuse
to approve in a SOC2 audit. The default the framework would generate — "give
the agent these 12 tools and let the LLM choose" — works for personal
projects and fails for B2B SaaS contracts.

What an expert tells the agent:

"For our customer PeoplePulse (mid-market HRIS, ~800 enterprise customers,
$45M ARR), we are designing the tool registry for their support agent.

Before generating any tool schemas, build me this analysis as a table:

For each candidate tool, I need:
1. The tool name and a 1-line description
2. Read-only or write? If write, what entities does it mutate?
3. Reversibility: automated rollback possible / manual cleanup required /
   irreversible?
4. Worst-case business impact if the LLM calls it with wrong arguments
   (in dollars or in 'customer-visible incident severity')
5. Should this require human approval before execution? If yes, what's
   the approval mechanism (Slack, email, in-app)?
6. Customer-tier gating: which contract tiers should have access?
   (Standard / Pro / Enterprise / regulated-vertical-only)

Candidate tools to analyze:
- get_user_by_email
- get_user_login_history
- check_sso_configuration
- reset_user_password
- unlock_user_account
- get_company_subscription_status
- list_failed_payments
- issue_refund
- update_billing_email
- create_support_ticket
- escalate_to_human_csm

Do NOT generate tool schemas yet. I need the analysis table first.
After I review and adjust the table, we'll generate schemas only for the
tools that pass the analysis — and for write tools, we'll generate the
approval-gate code paths separately."

REALITY CHECK
If you ignore this concept:
- The coding agent generates 11 tool schemas in one go. You ship them. Three
  weeks later, the agent issues a $4,200 refund to a customer who asked
  about a $42 charge — the LLM passed the wrong argument and there was no
  approval gate. Your customer's CFO is on the phone with your CEO.
- The agent has read tools and write tools mixed in the same registry with
  no tier gating. A Standard-tier customer (not contractually entitled to
  automated writes) gets write actions executed against their data. SOC2
  audit finding. Renewal at risk.

Tool granularity is a contract-level decision. The framework will execute
whatever you give it.

AI CODING AGENT MOMENT #2: Stopping condition design for the agent loop

Why the framework cannot do this alone:
The default ReAct-style agent loop has no inherent stopping condition beyond
"the LLM stops calling tools." The LLM's judgment of "I'm done" is shaped
by the system prompt. A coding agent generating boilerplate won't know your
B2B SaaS use case's specific signals for "the agent has enough information
to answer" or "the agent should escalate to human" — those are domain
decisions, not framework defaults.

What an expert tells the agent:

"For PeoplePulse's support agent, the default 'LLM decides when to stop'
behavior is wrong for our use case. Build the stopping logic explicitly.

The agent should stop and respond to the user when ANY of these are true:
1. The agent has called all tools needed to answer the user's exact question
   AND the answer is fully grounded in tool results
2. The agent has called 8 tools without converging on an answer
   (max_iterations safety stop)
3. The agent encounters a tool error twice in a row on the same tool
   (probable infrastructure issue, escalate)
4. The user's intent is ambiguous and the agent has identified TWO or more
   plausible interpretations (ask for clarification, do not guess)
5. The action required would be a write tool but the user's eligibility for
   that action is unclear (escalate to human CSM)
6. The agent is about to issue a customer-visible communication (email, in-app
   message) — pause for human approval regardless of confidence

For each stop condition, specify:
- The detection logic (what in the message history triggers it)
- The user-facing response template
- Whether it counts as 'resolved by agent' or 'escalated' in our metrics
- Whether it should log a specific audit event for our customer's compliance team

The system prompt should explicitly instruct the LLM about conditions 1, 4,
and 5. Conditions 2, 3, and 6 should be enforced in the orchestration code,
NOT trusted to the LLM. Show me the prompt section and the orchestration
code separately."

REALITY CHECK
If you ignore this concept:
- The agent loops 23 times on a malformed customer question, racks up
  $14 in API cost on a single ticket, and eventually returns "I'm sorry,
  I couldn't help" — after eight minutes of latency. Customer churns.
- The agent decides on its own to send a customer-visible email confirming
  a subscription change it inferred from context. The change wasn't what
  the customer wanted. The email is now in the customer's audit log.

Stopping conditions are correctness infrastructure, not engineering hygiene.

AI CODING AGENT MOMENT #3: Tool description disambiguation for similar tools

Why the framework cannot do this alone:
A coding agent will generate tool descriptions that *sound* clear in
isolation. The failure mode is when the agent has 4 tools that all sound
plausible for a given user intent — get_user_account, get_customer_account,
get_billing_account, get_organization_account — and the LLM picks the
first plausible match. Disambiguating these requires understanding how
your customer's domain actually distinguishes the entities, which the
coding agent doesn't know.

What an expert tells the agent:

"The current tool registry has these four lookup tools and the LLM is
confusing them in production (tool selection accuracy on this category
is 67% on our eval set):

- get_user_account
- get_customer_account
- get_billing_account
- get_organization_account

For PeoplePulse's domain, here is the actual entity model:
- A 'user' is an individual employee at one of PeoplePulse's customers
- A 'customer' is the company that pays PeoplePulse (e.g., 'Acme Corp')
- A 'billing account' is the financial record attached to a customer
  (one customer can have multiple billing accounts for different
  divisions)
- An 'organization' is a container for multiple customers under a
  parent company (e.g., 'Acme Holdings' contains 'Acme Corp' and
  'Acme EU')

Rewrite each tool's description so that:
1. The first sentence explicitly contrasts it with the other three tools
   ('Use this when you need X, NOT when you need Y or Z')
2. The parameter names use domain-specific vocabulary, not generic 'id'
   (employee_email_or_id, customer_company_name_or_id, etc.)
3. Each description includes ONE example of correct use AND ONE example
   of a wrong use that previously caused confusion
4. The description ends with 'If the user said [these phrases], use this
   tool. If they said [these phrases], use [other tool] instead.'

Then, generate a 30-question eval set specifically targeting the boundary
cases between these four tools — questions where a junior engineer might
pick the wrong one. Run it before we redeploy."

REALITY CHECK
If you ignore this concept:
- A CSM asks the agent about Acme Corp's billing. Agent calls get_user_account
  with 'Acme Corp' as the parameter, gets back null, tells the CSM "no record
  found." The CSM concludes Acme Corp isn't a customer. Acme Corp is in fact
  a customer — the agent just called the wrong lookup.
- Tool selection accuracy stays at 67% across all four lookups. Your customer's
  team builds workarounds: prefixing every question with "in our billing system,
  ..." to nudge the LLM. The agent's UX degrades into a constrained command
  language. The "AI" loses its value proposition.

When tools sound similar, the LLM will mix them. Disambiguation is a
description-engineering problem, not a model problem.

AI CODING AGENT MOMENT #4: Tool result interpretation audit

Why the framework cannot do this alone:
The framework wires up tool calls and tool results correctly. What the
framework can't catch is when the LLM mis-interprets a structurally correct
tool result — reading a timestamp wrong, mis-counting items in a list,
mis-parsing a status enum. The audit for this is specific and not in any
framework's defaults.

What an expert tells the agent:

"Build a tool-result-interpretation audit for the support agent. We
suspect the LLM is mis-reading some tool results.

For 100 random conversations from last week's logs:

1. For each tool call in the conversation, extract:
   - The tool name and arguments
   - The full tool result (raw)
   - The agent's next message that referenced the tool result

2. For each (tool result, agent message) pair, classify:
   - FAITHFUL: agent's message accurately reflects the tool result
   - DROPPED: agent's message ignored important fields in the result
   - MIS-PARSED: agent's message states something contradicted by the result
   - INTERPRETED-PLAUSIBLY: agent added inference not in the result, but
     the inference is reasonable
   - INTERPRETED-WRONGLY: agent added inference not in the result, and
     the inference is wrong

3. For each tool, report the distribution. Tools with >5% MIS-PARSED or
   INTERPRETED-WRONGLY need their result format reworked.

4. For tools with high mis-interpretation rates, propose:
   - Result format changes (flatten nested JSON, expand enums to full text,
     pre-compute derived fields)
   - Description additions ('the field X is in seconds, not milliseconds')
   - Whether to add a result-summary tool that the LLM calls on the result
     before using it

This is not a one-time audit. Schedule it weekly in production. Surface
the results to the engineering team in a dashboard."

REALITY CHECK
If you ignore this concept:
- The 'last_login' field in your tool result is a Unix timestamp. The LLM
  reads it as '1698732847' and tells the customer 'your last login was
  in the year 1698.' Customer assumes the agent is broken.
- The 'status' enum returns 'PARTIAL_REFUND_PENDING_APPROVAL'. The LLM
  shortens this to 'refund pending' in its response. Customer reads
  'refund pending' as 'refund will arrive soon' and waits a week before
  realizing it required their CFO's approval that no one requested.

Tool calls succeeding doesn't mean tool *answers* are correct. Audit
interpretation, not just execution.