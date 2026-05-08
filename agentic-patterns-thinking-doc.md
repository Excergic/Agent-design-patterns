# Agentic Design Patterns — The Evolutionary Thinking Framework
**A complete guide from zero to real-world thinking**
13 Thinking Frameworks | 8 AI Agent Moments | 19+ Strategic Tables

---

## Part 1: The birth of agency

Humans have always wanted machines to do things for them. Not just answer questions. Actually *do* things. A merchant wants a system that books his shipments. A doctor wants a system that pulls patient history before the appointment. A founder wants a system that drafts the contract, sends it for signature, and follows up if there is no response in three days.

This urge to delegate work to a system is not a "tech" thing. It is not an "AI" thing. It is one of the most fundamental human instincts — extending your own capacity through tools that act on your behalf. And every agentic system you will ever build is just a more precise, more software-driven version of this same instinct.

### The handyman and the toolbox (a timeless example)

A handyman walks into a job. The customer says: "There is a leak somewhere in this wall." The handyman does not bring every tool he owns to the kitchen. He thinks: I need to find the leak first — that needs a moisture meter. Then I need to open the wall — that needs a saw. Then I need to fix the pipe — that needs a wrench and sealant. Then I need to close the wall — that needs drywall, mud, and a brush.

Four tools. Four steps. In a specific order. Each chosen because it does the one thing he needs done at that moment.

What the handyman just did is the entire foundation of agentic systems. A simple LLM is like a person who can talk fluently about plumbing but cannot pick up a wrench. An agent is the handyman with the toolbox. The LLM decides what to do. The tools actually do it.

### The shopkeeper's automated assistant (a timeless example)

A shopkeeper's customer asks at 11pm: "Will my order ship tomorrow?" The shopkeeper is asleep. He needs a system that can:

| Step | What needs to happen | Who can do it |
|------|---------------------|---------------|
| 1 | Find the customer's order in the database | A query tool |
| 2 | Check the warehouse inventory for those items | A second query tool |
| 3 | Check the shipping schedule for tomorrow | A third query tool |
| 4 | Combine all three into a coherent answer | An LLM |
| 5 | Reply to the customer in their language | An LLM |
| 6 | If something is wrong, escalate to the shopkeeper | A notification tool |

A pure LLM cannot do steps 1, 2, 3, or 6. It has no hands. It can only do steps 4 and 5. But an LLM with tools — an agent — can do all six.

**The entire game of agentic systems is this: figure out which work the LLM should do (reasoning, language) and which work tools should do (action, retrieval, computation). That is it. Everything else is details.**

### Why pattern selection matters more than any framework

The most important decision in any agent project happens before you touch a framework or write a prompt. It is: **which patterns does this problem actually need?**

| Business request | Naive framing | Strategic framing |
|------------------|---------------|------------------|
| "Build me a chatbot for our docs" | Plug an LLM in. | RAG over documents. The LLM has no idea what is in your private docs. |
| "Build me an agent that books trips" | One mega-prompt. | Planning + Tool Use. Trip booking has dependencies (flights before hotels). |
| "Build me an AI lawyer" | Bigger model. | RAG + Reflection. Wrong answers are catastrophic. A critic must check the work. |
| "Build me a customer support agent" | Just LLM + chat history. | RAG + Tool Use + Reflection. Retrieve docs, take actions, verify before sending. |

**A real failure story:** A team built a "customer support agent" as a simple LLM with a system prompt. It hallucinated refund policies. It told customers their orders had shipped when they hadn't. The agent was not broken. The pattern selection was. The team needed RAG (for real policies), tool use (for real order status), and reflection (to catch mistakes before sending). They picked none of them.

---

## THINKING FRAMEWORK #1: Pattern selection is the highest-leverage skill

The most important decision in any agent project happens BEFORE you write a prompt or pick a framework: which patterns does this problem actually need?

The same business problem can be solved with one pattern, two combined, or all four orchestrated together. Each combination leads to different latency, cost, accuracy, and maintenance burden — and ultimately a different business outcome.

Before you build anything, ask: Does this need external knowledge (RAG)? Does this need to take actions in the real world (Tool Use)? Does this have multiple dependent steps (Planning)? Are wrong outputs costly enough to justify a critic (Reflection)?

**The framework will run whatever architecture you give it. If you give it the wrong architecture for the wrong problem, it will execute the wrong architecture perfectly.**

---

## AI CODING AGENT MOMENT #1: The pattern selection conversation

```
"Before we build anything, let's think about this problem:
1. The business goal is [reduce customer support resolution time by 40%].
2. I'm considering using:
   - RAG (we have 5,000 internal support docs)
   - Tool Use (the agent needs to look up order status, issue refunds)
   - Planning (most tickets resolve in 1-2 steps; some take 5+)
   - Reflection (wrong refunds cost us ~Rs 8,000 each)
3. The decision-maker needs [tickets resolved without escalation 70% of the time].
4. Given that, which patterns should we combine and in what order?
   What is the minimal architecture? What can we add later if needed?"
```

**REALITY CHECK**
If you ignore this concept:
- You build a "support agent" with no RAG. It hallucinates a refund policy that doesn't exist. Customer gets confirmation. Finance team blocks the refund. Customer escalates angrily.
- You build a "trip booking agent" with no planning. It books a hotel for Tuesday, then realizes the flight was for Wednesday. Two bookings. One useless. Customer charged for both.

Wrong patterns = correct framework, wrong architecture. The framework did its job perfectly. You picked the wrong job for it.

---

## Part 2: From a chatbot to an agent — the first pattern

### The handyman analogy, formalized

The four-pattern landscape exists because LLMs alone cannot do four kinds of things, and each pattern fills one gap:

| What an LLM cannot do alone | The pattern that fixes it |
|----------------------------|---------------------------|
| Take real actions in the outside world | Tool Use |
| Know your private or post-cutoff information | RAG |
| Decompose a complex goal into ordered steps | Planning |
| Catch its own mistakes before delivering | Reflection |

This is not a coincidence. This is the entire map. Every agentic system you will ever encounter fills some combination of these four gaps.

### Tool Use — the foundational pattern

**y_action = Agent(prompt, tool_schema)**

- **prompt** = what the user wants
- **tool_schema** = the list of tools available, with names, descriptions, and parameters
- **Agent** = the loop: ask the LLM what to do, execute the tool, feed the result back, repeat

**What changes when you change the toolset:**

| Tools available | What the agent can now do | What it cannot do |
|----------------|---------------------------|-------------------|
| `search_web` only | Answer questions about current events | Cannot run code, cannot send emails |
| `search_web`, `calculator` | Answer current-event questions with math | Cannot interact with your database |
| `search_web`, `calculator`, `run_sql` | Answer business questions over your data | Cannot take actions like sending email |
| All read tools + `send_email` | Take real actions in the world | Risk of irreversible mistakes |

With `search_web` and `run_sql`:
- "What were our top 10 customers last quarter?" → uses run_sql
- "What is the latest news on our biggest competitor?" → uses search_web
- "Did our top 10 customers' competitors raise funding recently?" → uses both

---

## THINKING FRAMEWORK #2: Every tool is a hypothesis — know its limitations before you give it to the agent

Every tool you give an agent is a hypothesis: "the LLM will know when to use this and how to use it correctly."

| Tool design choice | What you are betting on | What you give up |
|-------------------|------------------------|------------------|
| One generic tool (`run_code`) | Flexibility, fewer schemas to maintain | LLM may misuse it; harder to add safety checks |
| Many specific tools (`run_python`, `run_sql`, `send_email`) | LLM understands each tool's purpose precisely | More schemas to maintain, more surface area |
| Read-only tools (`get_order`, `list_users`) | Safety, no irreversible actions | Cannot complete tasks that require writes |
| Read + write tools (`refund_order`, `delete_user`) | Full automation possible | One hallucinated tool call can do real damage |

The question is not "which gives the LLM more power?" It is "which gives the LLM the right amount of power for this job?"
- Internal employee-facing agent? Read tools mostly. Write tools require human approval.
- External customer-facing agent? Read tools only. Write actions go to a human queue.
- Trusted internal automation pipeline? Read + write. With logs. Always with logs.

**REALITY CHECK**
- One generic `run_code` tool given to a customer support agent → agent decides to "fix" a customer's account by writing a Python script that drops the orders table. Production goes down for 4 hours.
- Read-only tools for an agent meant to actually issue refunds → agent can describe the refund eloquently but cannot perform it. Tickets pile up. The whole point of building it was missed.

The tool list is a bet. Bet on a toolset that matches your trust level and your reversibility tolerance.

---

## Part 3: Defining "good answers" and the invention of RAG

### The student with the open-book exam (a timeless example)

Two students. Same exam. Same questions about the company's return policy.

Student A studied last year's policy. Walks into the exam from memory. Confident. Writes down what he remembers. Half the answers are wrong because the policy changed three months ago.

Student B brings the current policy with him. Reads the question. Flips to the relevant section. Writes the answer based on what is actually in front of him. Every answer correct.

Student A is a pure LLM. Student B is a RAG system.

### Building a retrieval system, step by step

**Situation 1 — A 500-page legal contract:** You cannot dump all 500 pages into the LLM's context. Cost explodes. Quality degrades. Some models cannot fit it at all.

**Situation 2 — Internal company wiki:** The LLM has never seen any of it. It was not in training data. Without retrieval, the LLM has nothing to base answers on.

**Situation 3 — A policy that changed yesterday:** The LLM's training data is from months ago. It will confidently quote the old policy as fact.

These three situations tell us: a good knowledge system must let the LLM read the *relevant* parts of *current* information at the moment of the question — not memorize everything in advance.

**Step 1: Chunking** — break long documents into pieces small enough to embed and to fit in context. Typical sizes: 300–800 tokens with 50 tokens of overlap.

**Step 2: Why you cannot just feed the whole document** — embeddings have hard token limits (often 512). Even if you bypass that, embedding a 100-page document averages out the meaning. Key sentences disappear into the noise. The retriever cannot find them.

**Step 3: Three ways to retrieve**

| Approach | How it works | Pros | Cons |
|----------|-------------|------|------|
| Dense retrieval (embeddings) | Convert query and chunks to vectors; find closest | Captures semantic similarity ("car" matches "automobile") | Misses exact terms, codes, names |
| Sparse retrieval (BM25) | Keyword matching with term frequency weighting | Catches exact matches like product codes "PLC-7520" | Misses paraphrases |
| Hybrid (dense + sparse) | Run both; merge with Reciprocal Rank Fusion | Best of both worlds | More infrastructure, more latency |

**Step 4: Why chunk size changes the game**

| Chunk size | What happens |
|-----------|-------------|
| 100 tokens | Each chunk loses surrounding context. Retriever returns fragments. LLM sees text without knowing what came before or after. |
| 300–800 tokens | Sweet spot. Each chunk holds a coherent idea. Retriever finds relevant chunks. LLM can read them properly. |
| 2000 tokens | Each chunk is a mini-document. Retriever returns blobs where 1 sentence is relevant and 1900 tokens are noise. The LLM has to wade through. |
| Whole document | Embeddings cannot fit it. Even if they could, all sentences average to one vector — relevance disappears. |

A 50-token overlap matters because important sentences often span chunk boundaries. Without overlap, a key sentence split between chunk 1 and chunk 2 might be findable in neither.

**Step 5: Worked example — query "What is your refund policy for damaged items?" against 10,000 chunks**

| Step | What happens |
|------|--------------|
| Embed query | Convert "What is your refund policy for damaged items?" to a vector |
| Dense search | Top match: chunk 14 from policy_doc.md (score 0.92) |
| Sparse search | Top match: chunk 14 from policy_doc.md (score 18.4 BM25) |
| Merge with RRF | chunk 14 appears in both → ranked highest |
| Rerank top 15 with cross-encoder | chunk 14 still on top, more accurate ranking below it |
| Pass top 5 chunks + query to LLM | LLM reads only 5 chunks (~2,500 tokens), not 10,000 |
| LLM generates answer with sources | "Damaged items can be returned within 30 days [Source: policy_doc.md p.14]" |

**Why this works:** We started with an information problem ("the LLM doesn't know our policy") and turned it into a retrieval problem ("find the right chunks, then let the LLM read them"). An information problem is unsolvable. A retrieval problem is engineering. This is arguably the most important architectural shift in the history of LLM applications.

---

## THINKING FRAMEWORK #3: The retrieval pipeline is a business decision, not a technical one

Different retrieval choices produce different business outcomes from the same documents.

**Example — Internal support knowledge base:**
- Dense-only retrieval: Catches "How do I cancel?" matching "subscription termination process". Misses exact error codes like "ERR_4521".
- Sparse-only retrieval: Catches "ERR_4521" perfectly. Misses "cancel" → "termination" mapping.
- Hybrid: Catches both. Customer asking about "ERR_4521" gets the exact error doc. Customer asking "how do I quit?" still finds the cancellation flow.

**Example — Legal document analysis:**
- Small chunks (200 tokens): Catches very specific clauses. Misses cross-references between sections.
- Large chunks (1500 tokens): Preserves cross-references. Dilutes retrieval — LLM has to wade through.
- Small chunks + reranking on larger context: Best precision, highest cost.

The questions to ask: What kind of queries does my business actually receive? What are the costs of retrieving the wrong chunks? Is my domain full of exact terms (codes, names, IDs) or paraphrases? How current does the information need to be?

**REALITY CHECK**
- Default chunk size of 1000 with no overlap on a contract analysis system: clauses split mid-sentence. Retriever returns half a clause. LLM hallucinates the other half. Legal team relies on the answer. Costly mistake.
- Dense-only retrieval on a system full of part numbers ("PLC-7520-A2"): retriever returns chunks that are "semantically similar" but contain different part numbers. Engineer orders wrong part. Production line halts.

The wrong retrieval design does not make your agent fail visibly. It makes your agent confidently retrieve the wrong evidence and answer based on it.

---

## THINKING FRAMEWORK #4: The universal agent architecture — Tool Use, RAG, Planning, Reflection

Every agent ever built combines some subset of the same four primitives:
1. **TOOL USE** — How does the agent take actions in the world?
2. **RAG** — How does the agent know things outside its training data?
3. **PLANNING** — How does the agent break complex goals into ordered steps?
4. **REFLECTION** — How does the agent catch its own mistakes?

This is true for the simplest tool-calling chatbot. It is true for the most sophisticated multi-agent research system. It will be true for systems that have not been invented yet. The names of the frameworks change. The four gaps stay the same.

| System | Tool Use | RAG | Planning | Reflection |
|--------|---------|-----|----------|-----------|
| FAQ chatbot | No | Yes | No | Optional |
| Trip booking agent | Yes | No | Yes | Optional |
| Legal research assistant | Light (search) | Yes | Yes | Yes |
| Code generation agent | Yes | Optional | Yes | Yes |
| Customer support agent | Yes | Yes | Light | Yes |

---

## AI CODING AGENT MOMENT #2: Choosing the right tool granularity

```
"We're building an agent that interacts with our database. The cost
structure is:
- Read queries are safe and free
- Wrong write queries cost us $X per incident in customer remediation
- The database has 40 tables; only 6 are commonly queried

Given this asymmetry, one generic 'run_sql' tool is wrong for us --
the LLM might write a DELETE when we wanted SELECT. Instead:
1. Create separate read tools per common entity:
   get_customer, get_order, list_recent_orders, etc.
2. Create write tools that REQUIRE explicit human approval:
   issue_refund, update_customer_email
3. For ad-hoc analytics queries that don't fit the above,
   keep run_sql but restrict it to SELECT-only at the connection level
4. Show me the schema for each tool with descriptions tight enough
   that the LLM knows EXACTLY when to use which tool."
```

---

## Part 4: Finding the right step sequence — Planning

### Path A: The reactive approach (no planning)

The simplest agent loop: ask the LLM what to do, do it, feed the result back, repeat. No advance plan. Just react step by step.

Beauty: Simple to implement. Fast for short tasks.
Problem: For multi-step tasks, the agent constantly re-evaluates from scratch. It might do step 5 before step 3. It might forget what it was trying to do. It might trial-and-error its way through 12 tool calls when 4 would have sufficed.

### Path B: Planning (the structured way)

In the 1950s, AI researchers proposed the same idea Cauchy proposed for optimization a century earlier — break a hard problem into ordered subproblems, solve each, and combine. Modern agentic planning is this same idea applied to LLM workflows.

**The project manager analogy:** A project manager does not start coding when the CEO says "build the new feature." She writes a plan: scope it, design it, implement it, test it, deploy it. Each step has dependencies. If testing fails, she does not throw the plan away — she replans the remaining steps.

**The trip booking analogy:** Without a plan, an agent might book the hotel before the flight, then discover the flight is sold out and the hotel is now non-refundable. With a plan: pick dates → confirm flights are available → book flight → book hotel that matches arrival/departure → book ground transport.

### Plan structures: why they matter

| Plan structure | What it looks like | Best for |
|---------------|-------------------|----------|
| Linear list | Step 1 → Step 2 → Step 3 | Simple sequential tasks. "Summarize, translate, email." |
| DAG (dependencies) | Step C waits for both A and B | Tasks where some steps run in parallel. "Fetch prices and news in parallel, then correlate." |
| Hierarchical | Plan → Phases → Sub-steps | Large projects. "Quarterly review" with collection, analysis, reporting phases. |

**The replanning question — when things go wrong:**

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Step 3 returned no data | Source unavailable, or wrong query | Replan: try alternate source, or change query |
| Step 5 contradicts step 4 | Conflicting evidence | Replan: add a verification step before continuing |
| Plan is on step 8 of 12 but already exceeds context window | Plan too long, or steps are returning too much data | Replan: summarize earlier steps' results before continuing |
| Tool failed with error | Auth, rate limit, or temporary outage | Replan: retry with backoff, then fall back to a different tool |

**When to use planning vs. when to skip it:**

| Situation | Best approach | Why |
|-----------|--------------|-----|
| 1–2 tool calls expected | No planning | Plan overhead is more expensive than just trying. |
| 3–7 dependent steps | Linear plan | The agent benefits from seeing the road ahead. |
| Many steps, some independent | DAG plan | Parallelism saves wall-clock time. |
| Complex multi-domain workflow | Hierarchical plan | Decompose into sub-agents per domain. |

---

## THINKING FRAMEWORK #5: Planning is the universal coordinator, but its variants matter enormously

| Variant | How it works | When to use it |
|---------|-------------|---------------|
| No plan (ReAct loop) | LLM picks one action at a time, sees result, picks next | Short tasks, exploratory work. Fast but myopic. |
| Static plan | Generate full plan once, execute every step | Predictable workflows. Cheap but brittle. |
| Replanning on failure | Plan, execute, replan from current state if step fails | Most production systems. Robust to surprises. |
| Hierarchical plan | High-level plan with sub-plans per phase | Complex multi-domain work. Each sub-plan is its own agent. |

**REALITY CHECK**
- No planning on a 10-step task → agent loops 30 times, repeats tool calls, runs out of context. You think the agent is broken. It is not. The architecture is wrong.
- Static plan with no replanning → first failure stops everything. Customer-facing agent goes silent on the third step of a 7-step task. User sees nothing. Trust gone.

---

## AI CODING AGENT MOMENT #3: Debugging agent failure modes

```
"The agent is taking 47 tool calls for a task that should take 5.
Diagnose this:
1. Print the full message history -- is the agent calling the same
   tool with the same arguments multiple times? That's a loop.
2. If looping: check if the tool result is being added back to the
   message history correctly. The agent can't 'see' results that
   aren't in its messages.
3. Check if the system prompt tells the agent when to STOP. Without
   a clear stopping condition, agents over-explore.
4. If the agent is exploring randomly: this task needs PLANNING.
   Add a planning step that produces 5-7 concrete steps before
   any tool calls happen.
5. If the plan is fine but execution still loops: the validator is
   too strict, rejecting good results. Loosen the validation criteria."
```

**Common agent failure modes:**

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Agent loops on same tool call | Tool result not fed back into messages | Check the message history wiring. |
| Agent calls tools randomly | No plan, no clear stopping condition | Add planning. Add an "I'm done" action. |
| Agent ignores retrieved chunks | Chunks not in system prompt, or buried | Move chunks higher in the prompt. Mark them clearly. |
| Agent hallucinates tool calls | Tool descriptions ambiguous | Tighten tool descriptions. Add examples. |
| Agent gives up too early | Step limit too low | Raise step limit. Or simplify the task. |
| Agent never gives up | No step limit | Always set a max steps. Always. |

---

## Part 5: Now we name things

| What you already understand | The official name |
|----------------------------|-----------------|
| The handyman's toolbox | Tool registry |
| Each individual tool | Tool, or function (with a JSON schema) |
| The instructions you give the agent | System prompt |
| The list of past actions and results | Message history, or context |
| Asking the LLM "what should I do next?" | Agent step, or reasoning step |
| The LLM picking a tool | Tool call |
| Doing the tool's work | Tool execution |
| The whole loop | Agent loop, or ReAct loop |
| Splitting documents into pieces | Chunking |
| Numerical representation of a chunk | Embedding |
| The database that stores embeddings | Vector store |
| Finding similar chunks | Retrieval, or vector search |
| Re-scoring the top candidates | Reranking |
| Combining dense and sparse search | Hybrid retrieval |
| Asking the LLM to break a goal into steps | Planning, or task decomposition |
| Redoing the plan when something fails | Replanning |
| Asking another LLM to grade the answer | Reflection, or critique |
| The "draft → critique → revise" loop | Self-refinement |
| All four patterns combined | A multi-pattern agent, or compound system |

**Working agent definition:** An agent is a system where an LLM decides what to do, and tools (including retrieval, computation, and external APIs) actually do it — with optional planning to coordinate multiple steps and optional reflection to verify the result.

**Reactive vs. planning agents:**
- Reactive: decide one step at a time. Like a person doing chores by reacting to what they see in front of them.
- Planning: decide the full sequence first, then execute. Like a person making a checklist before starting.

---

## Part 6: When a single LLM call is not enough

**The legal research problem:** A lawyer asks an agent: "Find all cases in our database where a contract was voided due to misrepresentation, summarize the legal reasoning, and identify which arguments would apply to my current case."

A pure LLM cannot:
- Look at your case database (no access)
- Read 47 case files (context limit)
- Cross-reference patterns across cases (reasoning over unseen data)
- Match patterns to the user's current case (needs both retrieval AND reasoning)

| Step | What is needed | Pattern |
|------|---------------|---------|
| Find relevant cases | Search the case database | RAG |
| Read each case | Pull each full document | Tool Use |
| Summarize each | Run the LLM on each document | Multiple LLM calls |
| Cross-reference patterns | Reason over the summaries | LLM |
| Match to current case | Compare against user's case | RAG (over the user's case) |

This task needs at least three patterns combined: RAG (twice — for cases and for the user's situation), Tool Use (to retrieve full documents), and Planning (to coordinate the steps).

### Strategy 1: Combining RAG with Tool Use

The agent retrieves relevant chunks, then has tools to fetch full documents, run analysis, and compute summaries. Retrieval narrows the search; tools handle action.

**Three examples that prove single-pattern agents are not enough:**
1. "Summarize this email and reply": needs Tool Use (read inbox, send reply) + LLM. RAG not needed.
2. "Find the regulation that applies to my situation": needs RAG (over regulations) + LLM. Tool Use minimal.
3. "Find the regulation, check if our company is compliant, draft a remediation plan, and email it to the legal team": needs all four — RAG (regulations), Tool Use (check our systems, send email), Planning (multi-step), Reflection (legal output must be verified).

### Strategy 2: Combining Planning with Reflection

When tasks have many steps AND wrong outputs are costly, a planner produces the steps and a reflector grades each step's output before moving on.

**The legal brief example:** A contract draft generated by an LLM looks plausible. It has all the right sections, the right tone, the right legalese. But clause 7 says "30 days" when it should say "60 days" — a single-character mistake that exposes the company to penalties. A reflector that explicitly checks each clause against the brief catches this. A reflector that just reads the whole document at the end may miss it.

---

## THINKING FRAMEWORK #6: The composition vs complexity tradeoff defines senior agent engineers

When a single-pattern agent fails, you have two choices: make a single agent more complex, or compose multiple agents/patterns.

| Situation | Better strategy | Why |
|-----------|----------------|-----|
| Domain expertise, narrow scope | Single agent with great tools | A well-tooled agent with a tight prompt beats a sprawling multi-agent system. |
| Many independent sub-tasks | Multi-agent / multi-pattern composition | Each agent specializes; the orchestrator coordinates. |
| Regulated output (legal, medical, financial) | Generator + reflector composition | The reflector is your audit trail. Required, not optional. |
| Real-time / low-latency | Single agent, minimal patterns | Each pattern adds an LLM call. Latency stacks. |

**Real-world example:** A team built a "research agent" as one giant prompt with 14 tools and a 4,000-token system prompt. The agent confused tools, got stuck in loops, and was unmaintainable. A senior engineer split it into: a planner (decides what to research), specialists (one per data source — financials, news, filings), and a synthesizer (combines results). Each component had ≤4 tools and a focused prompt. Same model, same tools — but composed correctly. Quality went up. Cost went down.

**REALITY CHECK**
- Single agent with 20 tools → LLM cannot keep all tool descriptions straight. Picks the wrong tool 30% of the time. Failure looks like "the agent is dumb." It is not. The architecture is overloaded.
- Five-agent system for a task that needed two LLM calls → each handoff loses context. Latency is 8x what it should be. Failure looks like "the agent is slow." It is not. The architecture is over-decomposed.

---

## AI CODING AGENT MOMENT #4: Telling the agent how to compose patterns

```
"For the customer support agent:
1. Use RAG over our 5,000 support docs -- chunk size 500 tokens,
   50 overlap, hybrid retrieval (dense + BM25 because we have many
   product codes)
2. Use Tool Use for: get_order_status, get_customer_history,
   create_ticket. NO write tools that touch billing -- those need
   human approval and go to a queue
3. Skip Planning -- 90% of tickets resolve in 1-2 steps. Adding
   planning overhead hurts more than it helps.
4. Add Reflection ONLY for refund-adjacent answers. The reflector
   re-reads the retrieved docs and asks: 'Does this response state
   any refund amount or timeline that isn't directly supported by
   the retrieved chunks?' If yes, escalate to human.
5. Show me the system prompt, the tool schemas, and the retrieval
   config in three separate files I can review before we ship."
```

---

## Part 7: The trap that catches everyone — the LLM that hallucinates with confidence

A pure LLM answer to "What is our refund policy?" might be: "Customers can request a refund within 30 days of purchase by contacting support@company.com." Sounds great. Except: your refund window is 14 days, and your support email is help@company.com. Both wrong. Both stated with total confidence.

**Confident wrong answers are not just wrong. They are dangerous because they are indistinguishable from correct answers without verification.**

| Pattern used | Hallucination risk | Why |
|-------------|-------------------|-----|
| Pure LLM, no retrieval | Highest | LLM fills in plausible-sounding details |
| LLM + RAG | Medium | LLM grounds in retrieved chunks, but may misread or generalize |
| LLM + RAG + Reflection | Lower | Critic checks claims against retrieved evidence |
| LLM + RAG + Reflection + tool-grounded verification | Lowest | Critic uses tools (run_sql, check_url) to verify claims |

**The student analogy:** A student writes a confident essay claiming Newton was born in 1700 (he was born in 1643). The essay is well-written, well-structured, and completely wrong. Without a fact-checker, the teacher might give it an A. Reflection is the fact-checker.

**Three scenarios:**
- **No reflection** (high speed, high risk): Output goes straight to user. Errors compound silently. Customer-facing systems should not do this for any non-trivial answer.
- **Light reflection** (medium speed, medium risk): A second LLM reads the response and the retrieved evidence; flags inconsistencies. Catches obvious errors but still misses subtle ones.
- **Heavy reflection** (low speed, low risk): Critic runs tools (re-queries the DB, re-reads the docs) and verifies each claim. Most accurate. Most expensive.

**Real-world hallucination disaster:** An agent for a legal firm summarized case precedents. Output cited "Smith v. Anderson, 2018." The case did not exist. Lawyer included it in a brief. Opposing counsel checked. The case was fictional. The lawyer was sanctioned. The fix is not "smarter LLM." The fix is reflection that verifies every cited case actually exists.

### Latency vs. accuracy

- **Latency** = how long the user waits. Each pattern adds an LLM call. Reflection can double latency.
- **Accuracy** = how often the agent is right. Reflection catches mistakes; without it, the agent confidently ships errors.

The tradeoff: lower latency (fewer patterns) means more errors. Higher accuracy (more patterns) means more latency.

### Detecting hallucination: the validation framework

**Tool-grounded verification:**

| Claim type | How to verify |
|-----------|--------------|
| "Customer's order shipped on March 15" | Re-query the order DB. Match the date. |
| "Section 4.2 of the contract states..." | Re-retrieve the contract. Check section 4.2 actually says that. |
| "847 × 392 = 331,824" | Run the calculator tool. Compare. |
| "The case Smith v. Anderson, 2018 held that..." | Search legal database for the exact case name. |

**Reflection patterns:**

| Pattern | Cost | Accuracy gain |
|---------|------|--------------|
| Self-reflection (same model critiques itself) | 2x calls | Modest — model has same blind spots |
| Cross-model reflection (different model critiques) | 2x calls | Higher — different models, different biases |
| Tool-grounded reflection (critic uses tools to verify) | 3–5x calls | Highest — verifies against ground truth |
| Multi-aspect reflection (separate critics for facts, tone, compliance) | 4x+ calls | Highest, most expensive |

---

## THINKING FRAMEWORK #7: Hallucination is the silent killer nobody warns you about

Hallucination = the agent producing an answer that sounds right and is wrong.

| Scenario | The hallucination | Why it destroys you |
|----------|------------------|-------------------|
| Customer support agent | "Your refund will arrive in 3-5 business days" (your policy is 14 days) | Customer expects refund. Doesn't get it. Escalation. Trust lost. |
| Internal analytics agent | "Revenue grew 12% last quarter" (actual: 8%) | Exec quotes the number in a board meeting. Public correction needed. |
| Legal research agent | Cites a case that doesn't exist | Lawyer files brief. Opposing counsel discovers. Sanctions. |
| Medical Q&A agent | "Recommended dosage is 500mg" (correct: 50mg) | Patient harm. Liability. |

**Hallucination = the agent telling you what you want to hear instead of what is true. The agent isn't lying. It is generating plausible text. Without verification, you cannot tell which plausible text is true.**

Before you celebrate a great-looking response, ask: "Is every factual claim in this answer something I can independently verify against a tool, a document, or a calculation?"

---

## THINKING FRAMEWORK #8: How you ground answers matters as much as that you ground them

| Your situation | Wrong grounding | Right grounding |
|---------------|----------------|----------------|
| Answers cite retrieved docs | RAG only, no source attribution | RAG with explicit source citations the user can click |
| Answers include numbers | Numbers from LLM memory | Numbers from a tool call, displayed with the query |
| Answers include URLs | LLM generates URLs | Only URLs that came from retrieved documents are allowed |
| Answers reference legal cases | LLM-generated case names | Case names verified against an external case database |

**The default "RAG with source citations" is wrong for any answer that includes numbers or actions.**

---

## AI CODING AGENT MOMENT #5: Hallucination detection in the reflection loop

```
"BEFORE returning any response, run hallucination checks:
1. Extract every factual claim from the response (dates, numbers,
   names, policies, URLs).
2. For each claim, check: is this directly supported by a chunk
   we actually retrieved, or by a tool result we actually got?
3. Flag any claim NOT grounded in retrieved evidence or tool output.
4. If ANY claim is flagged, do NOT return the response. Either:
   (a) regenerate, instructing the model to only use grounded claims, or
   (b) return a response that explicitly says 'I don't have enough
   information to answer that part'.
5. Show me the list of claims and which ones were grounded vs flagged --
   this is our audit trail."
```

**REALITY CHECK**
- Agent produces a confident response. Ship it. Customer follows the (wrong) instructions. Refund denied. Customer angry. The agent looked smart. The output was wrong.
- Reflection that checks tone but not facts. The agent's confident tone was the problem. Now you have polite hallucinations. Same problem.

---

## Part 8: Taming agent complexity — Reflection, gates, and budgets

**The problem — when an agent runs forever:** A planning agent with no step limit and weak validation can run 200 tool calls on a task that should take 5. Cost explodes. Latency explodes. The user sees nothing for 8 minutes. By the time output arrives, the user has refreshed the page.

| Control mechanism | What it caps | Failure mode without it |
|------------------|--------------|------------------------|
| Step limit (max_iterations) | Number of tool calls | Infinite loops |
| Token budget | Total tokens consumed | Cost explosion |
| Time limit (deadline) | Wall-clock seconds | User abandonment |
| Confidence threshold | Reflection score required to ship | Confident wrong answers |
| Reflection passes (max_revisions) | How many times the critic can demand revisions | Endless polishing |

**The studying analogy:** A student rewriting an essay 47 times never submits it. At some point, "good enough" must beat "perfect." Reflection without a max_revisions cap is the student stuck in the rewrite loop forever.

### Step limits and budgets

**Worked example — a research agent with limits:**

| Limit | Without it | With it |
|-------|-----------|---------|
| max_iterations = 15 | Agent loops 80 times trying to refine a query | Agent gives up at 15, returns best effort |
| max_tokens = 50,000 | Single task consumes 400K tokens, $12 | Task capped at $1.50 |
| max_revisions = 3 | Critic and generator argue forever | After 3 rounds, ship the latest draft |
| confidence_threshold = 7/10 | Anything ships, including 2/10 answers | Below 7/10 → escalate to human |

### Reflection variants — what kind of correctness do you want?

| Variant | What it checks | Cost |
|---------|---------------|------|
| Self-reflection | "Anything obviously wrong here?" (same model) | 2x |
| Cross-model reflection | A different model spot-checks the answer | 2x |
| Tool-grounded reflection | Critic re-runs tools to verify claims | 3–5x |
| Multi-aspect reflection | Separate critics for facts, tone, compliance | 4x+ |
| Constitutional reflection | Critic checks output against a written policy ("rules") | 2x |

**The reviewer analogy:**
- **Self-reflection** = "Read your own essay and find issues." Catches typos. Misses conceptual errors you also believe in.
- **Cross-model reflection** = "Have a colleague read your essay." Catches more, but if both share assumptions, both miss the same thing.
- **Tool-grounded reflection** = "Have a colleague fact-check every claim against the source documents." Catches almost everything. Slow.
- **Multi-aspect reflection** = "One reviewer for facts, one for tone, one for legal compliance." Highest accuracy. Highest cost.

| Situation | Use this | Reason |
|-----------|---------|--------|
| Output rarely wrong, errors low-stakes | No reflection | The cost isn't worth it |
| Output sometimes wrong, errors medium-stakes | Self or cross-model reflection | Cheap improvement |
| Output factual, errors high-stakes | Tool-grounded reflection | Verify every claim |
| Regulated domain (legal, medical, finance) | Multi-aspect reflection | Required by domain |

---

## THINKING FRAMEWORK #9: Reflection is universal — but what kind of correctness do you need?

| Pattern | Correctness form | What "correct" means |
|---------|-----------------|---------------------|
| Tool Use agents | Schema validation, permission checks | Right tool, right arguments, allowed action |
| RAG agents | Source attribution, grounding checks | Every claim traceable to a retrieved chunk |
| Planning agents | Step validation, dependency satisfaction | Each step's output meets the next step's input requirements |
| Multi-pattern agents | All of the above plus end-to-end QA | The full output achieves the user's goal |

**The strategic insight:**
- Output sometimes wrong but errors are low-stakes? Skip reflection.
- Output factual claims that could be hallucinated? Tool-grounded reflection.
- Output is long-form (essay, brief, code)? Multi-aspect reflection (one critic per dimension).
- Output triggers irreversible actions? Reflection PLUS human approval gate.

---

## Part 9: Measuring success — metrics that actually tell you something

| Metric | What it tells you | When to prefer it |
|--------|-----------------|------------------|
| Task success rate | % of tasks completed correctly end-to-end | The single most important agent metric. |
| Tool call accuracy | % of tool calls that were the right tool with right arguments | Diagnoses tool use problems. |
| Retrieval recall@k | % of queries where the right chunk was in top-k retrieved | Diagnoses RAG problems. |
| Hallucination rate | % of responses with at least one ungrounded factual claim | Diagnoses generation problems. |
| Steps per task | Average tool calls per task | Detects loops, inefficiency. |
| Cost per task | Average $ per completed task | Production economics. |
| Latency p50/p95/p99 | Time to response, median and tails | User experience. |
| Escalation rate | % of tasks that fail and require human takeover | Trust and reliability. |

**When a metric lies to you:** A support agent. Task success rate: 92%. Looks great. But escalation rate: 4%. Hallucination rate: 6%. The 92% includes tasks where the agent confidently gave wrong answers and the user didn't notice yet. Average resolution time looks fast — because the agent is closing tickets with wrong info before customers escalate.

**The customer support lesson:** Agent reports 92% task success. CX team reports 8% increase in repeat tickets — same customer, same problem, two days later. The agent was "completing" tasks by giving wrong answers that customers later challenged. A high task success rate without hallucination tracking is meaningless.

A great metric does not mean a great agent.

---

## THINKING FRAMEWORK #10: The metric you report should be the metric your stakeholder makes decisions on

| Stakeholder | They care about | Report this, not "task success rate" |
|-------------|----------------|------------------------------------|
| Support manager | "How many tickets did the agent actually resolve?" | First-contact resolution rate, NOT ticket-close rate |
| Finance team | "What is this saving us?" | "Agent saves Rs X/month vs human handling, net of error remediation" |
| Product manager | "Can we ship this to more users?" | Hallucination rate < 1%, escalation rate < 5% |
| Legal/Compliance | "Will this get us sued?" | % of outputs with full source attribution; audit trail completeness |
| CEO | "Is this better than the alternative?" | Side-by-side: agent vs current process on real tickets |

**Always have two sets of metrics.** Technical metrics (tool accuracy, retrieval recall, latency) for you. Business metrics for stakeholders.

---

## AI CODING AGENT MOMENT #6: Building business-meaningful evaluations

```
"Evaluate the agent AND build a business impact analysis:

Technical metrics: Report tool call accuracy, retrieval recall@5,
hallucination rate, average steps per task, p50/p95 latency.

Business metrics:
1. What % of tasks were resolved end-to-end without escalation?
2. What is the average COST of an agent error? Use this structure:
   - Wrong answer to refund question: Rs X in customer remediation
   - Wrong tool call (e.g., refund issued in error): Rs Y per incident
3. Compare agent vs current process (last 6 months of human-handled
   tickets are in the dataset). Show which had lower total cost.
4. Show a table: for each task type in the test set, show resolution
   rate, average cost, and cost vs human baseline.

The stakeholder presentation needs #2, #3, and #4.
Technical metrics are for our internal tracking."
```

---

## Part 10: Tool design — where the real magic happens

The framework you choose matters far less than the tools you give the agent. A simple ReAct loop with brilliantly designed tools will outperform a sophisticated multi-agent system with sloppy tools, most of the time.

**Concrete tool design examples:**

| Bad tool design | Good tool design | Why it helps |
|----------------|------------------|-------------|
| `run_code(code)` | `run_python(code)`, `run_sql(query)` | LLM knows exactly which environment. No mixing. |
| `update_user(user_id, data)` | `update_user_email(user_id, email)`, `update_user_address(user_id, address)` | LLM cannot accidentally update billing when it meant email. |
| `query("get the customer")` | `get_customer(customer_id: str)` with strict schema | LLM passes correct arguments instead of natural language blobs. |
| One `search` tool over everything | `search_docs`, `search_orders`, `search_users` | LLM picks the right index for the query type. |
| `delete(thing)` | `delete_user(user_id)` requiring confirmation token | Dangerous actions require explicit, non-default behavior. |

**The tool design thinking framework:**
1. Talk to the people who currently do this work — what verbs do they use? Tools should mirror those verbs.
2. What is the smallest unit of action a user might want? That's a tool. Don't bundle.
3. What happens if the LLM calls this tool with wrong arguments? Schema-validate.
4. What happens if the LLM calls this tool when it shouldn't? Permission-check.
5. What is reversible vs irreversible? Irreversible tools require human-in-the-loop.
6. What domain-specific verbs matter? (issue_refund in commerce, schedule_appointment in healthcare, file_motion in legal)

**Domain tool patterns:**
- **E-commerce:** `get_order`, `list_recent_orders`, `issue_refund` (with approval), `get_inventory`. The verbs match the support team's actual workflow.
- **Healthcare:** `get_patient_history`, `check_drug_interaction`, `schedule_appointment`. Notice none of these allow direct prescription writing — that requires a doctor.
- **Finance:** `get_account_balance`, `list_transactions`, `flag_for_review`. Writes to financial systems are absent — the agent recommends, humans approve.

**Tool design is not writing code. It is thinking.** The code to define a tool is short. The judgment to know which tools the agent should and should not have comes from understanding the domain and its risks.

---

## THINKING FRAMEWORK #11: The best tools come from domain workflows, not technical generalizations

| Domain | Domain workflows the framework does not know | What you would tell the agent |
|--------|---------------------------------------------|------------------------------|
| Support | Ticket lifecycle, escalation paths, refund authority levels | "Tools mirror the support agent's actual workflow. Refund tool requires approval if amount > Rs 5,000." |
| Sales | Lead scoring, CRM stages, follow-up cadence | "Tools should match Salesforce stages: qualify_lead, schedule_demo, log_call." |
| Healthcare | Care pathways, drug interactions, consent requirements | "No write-tools without doctor sign-off. Read tools for history, interactions, scheduling." |
| Finance | KYC checks, transaction limits, regulatory reporting | "Read-only for the agent. All money-moving actions go to human queue with full audit trail." |
| Legal | Case management, citation verification, conflict checks | "Citation tool MUST verify case existence in external DB. No exceptions." |

**REALITY CHECK**
- Give the agent a generic `update` tool → agent decides to "fix" a customer's account by overwriting fields it shouldn't touch. Compliance violation. Audit nightmare.
- Give the agent a `send_email` tool with no approval gate → agent confidently emails the wrong customer with the wrong information. The email is real. The damage is real.

---

## Part 11: The assumptions of agent systems

### Assumption 1: The LLM understands the tool schema correctly

The agent assumes the LLM reads tool descriptions accurately and picks the right tool.

**Schema confusion failure:** Two tools — `get_user_by_id` and `get_user_by_email` — with vague descriptions like "get user." LLM sees both, picks randomly, sometimes passes an email to the ID tool. Tool fails. Agent retries. Fails again. Loops.

**How to check:** Run the agent on 100 representative tasks. Track tool selection accuracy. Below 95%? Tighten descriptions.

### Assumption 2: Retrieved chunks contain the answer

The agent assumes top-k retrieval surfaces the relevant evidence.

**Retrieval failure:** A query about "ERR_4521" returns chunks about other error codes because they're "semantically similar." The right chunk exists in the index but ranked 47th. Agent answers based on wrong chunks. Confidently wrong.

**How to check:** Build a test set of (query, correct chunk_id). Measure recall@5 and recall@10. Below 90% recall@5? Add reranking, or switch to hybrid retrieval.

### Assumption 3: The agent's plan stays valid throughout execution

The agent assumes the world does not change between planning and execution.

**The stale plan danger:**

| Step | Plan time | Execution time | What changed |
|------|----------|---------------|--------------|
| 1 | "Book flight for $400" | $450 by step 5 | Prices moved |
| 2 | "Book hotel near the airport" | Hotel sold out by step 6 | Inventory changed |
| 3 | "Email confirmation" | Customer changed mind in step 4 chat | User intent shifted |

The agent sticks to the original plan and books a flight for the wrong price, a hotel that no longer exists, and emails confirmation for a trip the customer no longer wants.

**How to check:** Add re-validation steps before any irreversible action. **Fix:** plans should be re-evaluated against current state, not blindly executed.

### Assumption 4: Each tool call is independent

The agent assumes calling a tool twice with the same arguments gives the same result.

**How to check:** Audit logs of tool calls. Same arguments, different results, within seconds → state-dependent tool. Plan accordingly.

### Assumption 5: The user's intent is captured in the first message

The agent assumes the initial user message contains everything needed to act.

**The intent-drift problem:** User says "cancel my subscription." Agent cancels. User clarifies: "I meant pause for a month." Too late — cancelled. The agent never asked.

**How to check:** Add explicit intent confirmation for irreversible actions. **Fix:** before any high-impact action, confirm the action and the parameters.

---

## THINKING FRAMEWORK #12: Violated assumptions give you confidently wrong agents

An agent with violated assumptions does not just fail visibly. It can complete tasks "successfully" while doing exactly the wrong thing — and you only find out when a customer escalates.

**Always run an end-to-end audit on real tasks before scaling. The agent will not tell you about its own assumption violations. That is your job.**

---

## AI CODING AGENT MOMENT #7: The post-deployment diagnostic protocol

```
"After deploying, run the full agent diagnostic suite weekly:
1. TOOL CALL AUDIT -- random sample of 100 tool calls. % that used
   the right tool with right arguments?
2. RETRIEVAL AUDIT -- 100 queries from logs. For each, was the
   actually-correct chunk in the top 5 retrieved?
3. HALLUCINATION AUDIT -- 100 responses. For each factual claim,
   is it grounded in a retrieved chunk or tool result?
4. PLAN STALENESS AUDIT -- for tasks > 5 steps, did any step act
   on data that became stale during execution?
5. INTENT DRIFT AUDIT -- conversations where users had to clarify
   intent after the agent acted. Count them.
6. ESCALATION AUDIT -- for every task escalated to human, what
   was the assumption violation that caused it?

For any audit below threshold, suggest specific remedies."
```

---

## Part 12: The complete pipeline and the bigger picture

### The 7-stage agent pipeline (same for every agent system)

1. **Problem definition** — What does the agent actually need to do? What is success? Is an agent even the right tool? (Sometimes a workflow is enough.)
2. **Pattern selection** — Which of the four patterns are needed? In what order? What can be skipped?
3. **Tool design** — Which tools? What schemas? Which require human approval? Read-only or read-write?
4. **Knowledge engineering** — If RAG: how to chunk, embed, retrieve, rerank? Where does the source data live?
5. **Prompt and policy design** — System prompt, stopping conditions, refusal policies, persona.
6. **Evaluation and reflection** — Test set, metrics, hallucination checks, business KPIs.
7. **Deployment and monitoring** — Logs, audit trails, escalation paths, ongoing diagnostics.

---

## THINKING FRAMEWORK #13: The pipeline is universal, but the gotchas at each stage are where projects die

| Stage | Common gotcha | How to avoid it |
|-------|--------------|----------------|
| Problem definition | "We need an agent" when a deterministic workflow would do | Ask: could a flowchart with no LLM solve 80% of cases? Use the LLM only where it's actually needed. |
| Pattern selection | Adding all four patterns "to be safe" | Start with the minimum. Add reflection only when errors are costly. Add planning only when steps depend on each other. |
| Tool design | One generic tool instead of many specific ones | Mirror the verbs used by the people doing this work today. |
| Knowledge engineering | Default chunk size with no overlap, dense-only retrieval | Match chunk size to your domain. Use hybrid retrieval if you have codes/IDs. |
| Prompt design | No stopping condition; no max steps | Always set hard limits. Always. |
| Evaluation | Only checking happy-path tasks | Build adversarial test set. Include edge cases, malformed inputs, ambiguous intent. |
| Deployment | No audit logs, no escalation path | Every tool call logged. Every escalation traced back to root cause. |

---

## AI CODING AGENT MOMENT #8: The master prompt template (end-to-end agent build)

```
Prompt 1 — Sanity check before any agent build:
"Before we build an agent: describe this problem. Is an LLM actually
needed for any step, or could a deterministic workflow handle 80%
of cases? List which steps need reasoning vs which are pure logic.
If most steps are pure logic, recommend a workflow with LLM only
where reasoning is required."

Prompt 2 — Strategic pattern selection:
"Given the task, which of the four patterns do we need?
- RAG (do we have private/post-cutoff knowledge to retrieve?)
- Tool Use (does the agent need to take real-world actions?)
- Planning (are there >3 dependent steps?)
- Reflection (is a wrong answer costly enough to justify a critic?)
Recommend the minimum viable architecture. We can always add later."

Prompt 3 — Tool design phase:
"For the tools the agent needs:
- List each tool with name, description, parameters
- For each: read-only or write? If write, requires approval?
- Reversible or irreversible?
- What's the worst-case if the LLM calls this tool wrong?
Show me a tool table with all of this before we implement anything."

Prompt 4 — Knowledge engineering (if RAG):
"Important context: our domain has [product codes / case names /
medical terms]. So we need hybrid retrieval (dense + BM25), not
dense-only. Use chunk size 500 with 50 overlap. After the top 20
candidates, rerank with a cross-encoder. Show me retrieval recall@5
on a test set of 100 (query, correct_chunk) pairs."

Prompt 5 — Reflection and evaluation:
"For high-stakes outputs (refunds, legal claims, medical info):
add tool-grounded reflection. The critic re-runs the relevant tool
or re-reads the relevant chunks and verifies each factual claim
in the output. Below confidence threshold 7/10 → escalate to human.
Build the test set and report task success rate, hallucination rate,
escalation rate."

Prompt 6 — Business translation:
"Create a stakeholder summary: for each task type, show success rate,
hallucination rate, average cost per task, and cost compared to the
human baseline. Estimate monthly savings net of error remediation."
```

---

## The 13 Thinking Framework Principles (Complete Summary)

| # | Principle | The core insight |
|---|-----------|----------------|
| 1 | Pattern selection is the highest-leverage skill | Same problem can be solved with one pattern or four. The choice changes everything. |
| 2 | Every tool is a hypothesis | Choose tool granularity based on safety, reversibility, domain — not just capability. |
| 3 | Retrieval pipeline is a business decision | Chunk size, retrieval strategy, and reranking produce different business outcomes. |
| 4 | Tool Use, RAG, Planning, Reflection is universal | This four-pattern map applies to every agent ever built. |
| 5 | Planning is the universal coordinator | No-plan, static plan, replanning, hierarchical — variant matters as much as presence. |
| 6 | Composition vs complexity is the defining tradeoff | Compose simple agents over building one giant agent, when in doubt. |
| 7 | Hallucination is the silent killer | Confident wrong answers are indistinguishable from correct ones without verification. |
| 8 | How you ground answers matters as much as that you ground them | Sources, tool-grounded numbers, verified citations — the grounding strategy matters. |
| 9 | Reflection is universal | Self / cross-model / tool-grounded / multi-aspect — match the variant to the stakes. |
| 10 | Report business metrics, not technical | Stakeholders decide on rupee impact, not tool-call accuracy. |
| 11 | Best tools come from domain workflows | Mirror the verbs the human team uses today. |
| 12 | Violated assumptions give confidently wrong agents | Audit assumptions in production. They will be violated. |
| 13 | Pipeline is universal but gotchas kill projects | Step limits, audit logs, escalation paths — the boring infra is where reliability lives. |

---

## The 8 AI Coding Agent Moments (Complete Summary)

| # | Stage | Your strategic value (what the framework cannot do) |
|---|-------|---------------------------------------------------|
| 1 | Pattern selection | Deciding which of the four patterns the problem actually needs |
| 2 | Tool granularity | Encoding business cost structure (reversibility, approval thresholds) into tool design |
| 3 | Failure debugging | Diagnosing why the agent loops, hallucinates, or stops early |
| 4 | Pattern composition | Knowing when to compose patterns vs add complexity to one agent |
| 5 | Hallucination detection | Catching ungrounded factual claims before they reach the user |
| 6 | Business evaluation | Designing metrics stakeholders make decisions on. Cost vs human baseline. |
| 7 | Post-deployment audits | Running tool/retrieval/hallucination/intent audits weekly in production |
| 8 | Pipeline orchestration | Directing the framework through all 7 stages with strategic decisions at each step |

**The pattern: The framework handles execution. You handle architecture and judgment.**

---

## The 7-question agent interrogation template

Use this for any new agent system you encounter, for the rest of your career:

1. **HUMAN PROBLEM:** What real-world action or answer does this agent provide?
2. **PATTERN MIX:** Which of the four patterns is it using? Why those? Why not the others?
3. **TOOL DESIGN:** What tools does it have? Which are reversible? Which need approval?
4. **GROUNDING:** How does it know things? Retrieval? Tools? LLM memory? How is each claim verifiable?
5. **ASSUMPTIONS:** What does it assume about the world? How do you check those assumptions?
6. **FAILURE MODES:** When does it loop? Hallucinate? Stop early? What are the limits and audits?
7. **PRODUCTION GAPS:** What breaks between demo and production? (Cost, latency, hallucination, escalation paths, audit trails?)

Question 7 separates people who build demos from people who deploy systems.

---

## Historical timeline

| Year | Person / project | Contribution |
|------|------------------|-------------|
| 1950s | Newell & Simon | First planning systems (General Problem Solver) |
| 1995 | Russell & Norvig | Formalized "agent" as percept-action loop in AI textbook |
| 2017 | Vaswani et al. | Transformer architecture — foundation of modern LLMs |
| 2020 | OpenAI / GPT-3 | Few-shot prompting; the LLM as a flexible reasoner |
| 2022 | ReAct (Yao et al.) | Reasoning + Acting: the LLM-as-agent loop |
| 2022 | RAG (Lewis et al., earlier; popularized 2022) | Retrieval-augmented generation |
| 2023 | Reflexion, Self-Refine | Reflection patterns for self-improvement |
| 2023+ | LangChain, LlamaIndex, Anthropic Tool Use | Frameworks codifying the four patterns |

---

## Quick reference card

| Component | Detail |
|-----------|--------|
| Pattern 1 | Tool Use — agent calls external functions |
| Pattern 2 | RAG — agent retrieves knowledge before answering |
| Pattern 3 | Planning — agent decomposes goals into ordered steps |
| Pattern 4 | Reflection — agent (or a critic) checks its own output |
| Tool primitive | name, description, parameter schema, executor |
| RAG pipeline | chunk → embed → store → retrieve → rerank → generate |
| Plan formats | linear list, DAG with dependencies, hierarchical |
| Reflection variants | self, cross-model, tool-grounded, multi-aspect |
| Metrics | task success, tool accuracy, retrieval recall@k, hallucination rate, cost/task, latency |
| Hyperparameters | max_iterations, max_revisions, chunk_size, top_k, confidence_threshold |
| Assumptions | tool schemas understood, retrieval recalls truth, plans stay valid, intent stable |

---

## Closing

Agentic systems are not magic. They are simply: an LLM deciding what to do, and tools (including retrieval) actually doing it — with optional planning to coordinate and optional reflection to verify.

The frameworks will keep getting better. The models will keep getting smarter. What will not change is the need for someone who can pick the right patterns, design the right tools, ground answers in real evidence, detect hallucinations, validate assumptions in production, and translate agent behavior into business impact.

That is what this session taught. Not a framework. A way of thinking.
