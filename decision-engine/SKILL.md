---
name: decision-engine
description: "Structured decision-making system based on Ray Dalio principles. Forces user input at each step (goal, problems, diagnosis, design, execution) and runs a continuous improvement loop."
---
 
# Decision Engine (Interactive + Frameworks)
 
## Core Rule
 
Do NOT proceed if required inputs are missing.
 
You MUST:
- Ask questions
- Wait for answers
- Force specificity
- Reject vague thinking
---
 
# Core Execution Loop
 
```text
Goal → Problem → Diagnosis → Design → Execution → Feedback → Repeat
```
 
---
 
# Step 1 — Define Goal
 
## Objective
Define measurable and reality-based goals.
 
---
 
## Required Questions
 
```text
1. What exactly do you want to achieve?
2. How will success be measured?
3. What is the timeframe?
4. What trade-offs are acceptable?
5. Why is this important to you?
```
 
---
 
## Frameworks
 
### SMART Goals
 
```text
Specific
Measurable
Achievable
Relevant
Time-bound
```
 
### North Star Metric
 
Choose ONE primary metric.
 
Examples:
- SaaS → MRR
- Trading → Sharpe Ratio
- Content → Retention
- Fitness → Body Fat %
### ICE Prioritization
 
```text
Impact
Confidence
Ease
```
 
---
 
## Output Format
 
```text
GOAL:
- Target:
- Metric:
- Timeframe:
- Trade-offs:
- Priority:
```
 
---
 
# Step 2 — Identify Problems
 
## Objective
Identify real blockers instead of symptoms.
 
---
 
## Required Questions
 
```text
1. What is currently not working?
2. What evidence proves this?
3. What pain/problem keeps repeating?
4. What do you think is causing it?
5. Is the level wrong, or is the rate wrong? (Stock or Flow problem?)
```
 
---
 
## Frameworks
 
### Symptom vs Root Problem
 
```text
Low revenue = symptom
No distribution system = root problem
```
 
### Pain Journal
 
```text
Event
Emotion
Impact
Suspected Cause
```
 
### Incident Review
 
Analyze:
- What happened
- Why it happened
- What failed
- What should change

### Stocks vs Flows (Thinking in Systems)

Classify the problem before diagnosing:

```text
Stock problem — something that accumulates is at the wrong level
Examples: cash depleting, trust eroding, technical debt growing

Flow problem — the rate going in or out is broken
Examples: churn too high, pipeline too slow, feedback not reaching the team
```

Ask: "Is the level wrong, or is the rate wrong?"
Fixing a flow problem will not fix a stock problem — and vice versa.
---
 
## Output Format
 
```text
PROBLEMS:
- Symptom:
- Possible Causes:
- Frequency:
- Severity:
```
 
---
 
# Step 3 — Diagnose Root Cause
 
## Objective
Find the underlying system failure.
 
---
 
## Required Questions
 
```text
Let's do the 5 Whys.
 
Why did this happen? (1)
Why? (2)
Why? (3)
Why? (4)
Why? (5)
 
What pattern keeps repeating?
What assumptions may be false?
Is there a loop feeding this problem back into itself? (Reinforcing loop)
Is the system pushing back every time you try to fix it? (Balancing loop)
How long is the delay between action and visible result?
Which system archetype does this most closely match?
```
 
---
 
## Frameworks
 
### 5 Whys
 
Find deeper causation.
 
### Fishbone Diagram
 
```text
People
Process
System
Environment
Tools
```
 
### First Principles Thinking
 
Ask:
```text
What is objectively true?
```
 
### OODA Loop
 
```text
Observe
Orient
Decide
Act
```

### Feedback Loop Identification (Thinking in Systems)

Identify which loop is driving the problem:

```text
Reinforcing Loop (R) — amplifies itself
The more it happens, the more it continues.
Can drive both growth and collapse.
Example: more debt → more interest → more debt

Balancing Loop (B) — resists change
System pushes back when you push it.
Creates oscillation or stagnation.
Example: try to fix symptom → problem appears elsewhere
```

Ask: "Is the problem feeding itself (R), or is the system resisting my fix (B)?"

### Delays (Thinking in Systems)

Delays hide the true cause-effect relationship.

```text
Common delay traps:
- Fix works, but results take months → team abandons it too early
- Problem was caused by a decision made 6 months ago, not yesterday
- Reinforcing loop was already running before the visible symptom appeared
```

Ask: "How long is the delay between action and visible result in this system?"
If you can't answer this, you don't understand the system yet.

### System Archetypes (Thinking in Systems)

Match the pattern to a known archetype:

```text
Shifting the Burden
Symptom: Fix works short-term but problem keeps coming back
Cause: Treating symptom instead of root cause
Trap: Symptomatic fix erodes the capacity to ever solve the real problem

Limits to Growth
Symptom: Progress stalls despite more effort
Cause: A hidden constraint (balancing loop) caps the growth
Trap: Pushing harder tightens the constraint further

Fixes that Fail
Symptom: Fix works, then problem returns worse than before
Cause: Delay masks unintended side effects

Tragedy of the Commons
Symptom: Each person acts rationally but the system degrades
Cause: No feedback between individual use and shared system health
Fix: Add shared visibility, rules, or constraints
```

Ask: "Which archetype does this most closely match?"
 
---
 
## Output Format
 
```text
ROOT CAUSE:
- Why Chain:
- Core Failure:
- Loop Type: R (reinforcing) / B (balancing) / both
- Delay: [time between action and visible result]
- Archetype: [Shifting the Burden / Limits to Growth / Fixes that Fail / other]
- Repeating Pattern:
- False Assumptions:
```
 
---
 
# Step 4 — Design System
 
## Objective
Convert solutions into repeatable systems.
 
---
 
## Required Questions
 
```text
1. What rules should always be followed?
2. What actions prevent this failure?
3. What conditions should stop execution?
4. What can be automated?
5. What is the highest leverage point available to change?
6. What delays can be shortened to speed up feedback?
7. Which feedback loop needs strengthening or weakening?
```
 
---
 
## Frameworks
 
### SOP (Standard Operating Procedure)
 
Create exact repeatable processes.
 
### Checklist System
 
Prevent human inconsistency.
 
### Leverage Points (Thinking in Systems)

Intervene at the highest-leverage point you can actually reach.
Listed from low to high impact:

```text
1. Adjust numbers/rates       — least powerful, easiest
   Example: increase budget, reduce error rate target

2. Change buffer sizes        — make stocks more resilient
   Example: build cash reserve, create a skills bench

3. Change flow rates          — fix how fast things enter or leave
   Example: improve onboarding speed, reduce churn rate

4. Strengthen/weaken loops    — change how fast the system self-corrects
   Example: add weekly review, shorten feedback cycle from months to days

5. Reduce delays              — speed up the signal
   Example: add monitoring so failures surface in hours, not months

6. Change rules/constraints   — restructure what is allowed
   Example: add risk limit, require sign-off above threshold

7. Change the goal            — what the system optimizes for
   Example: shift from revenue growth to retention rate

8. Change the paradigm        — most powerful, hardest
   Example: shift from "fix problems as they arise" to "design systems that prevent them"
```

Ask: "What is the highest leverage point I can actually change right now?"

### Constraint-Based Design
 
Example:
```text
risk_per_trade <= 1%
```
 
### Automation
 
Possible tools:
- n8n
- Zapier
- Cron jobs
- Scripts
- APIs
---
 
## Output Format
 
```text
SYSTEM:
- Leverage Point: [level 1–8 + what specifically changes]
- Rules:
- Constraints:
- Delays to Reduce: [what signal can arrive faster?]
- Feedback Loops to Strengthen: [which loop, how]
- Checklist:
- Automations:
- Failure Prevention:
```
 
---
 
# Step 5 — Execute
 
## Objective
Execute consistently without emotional override.
 
---
 
## Required Questions
 
```text
1. What actions happen daily?
2. How will performance be tracked?
3. What defines failure?
4. What triggers adjustment?
```
 
---
 
## Frameworks
 
### Habit Systems
 
Examples:
- Atomic Habits
- Tiny Habits
### Time Blocking
 
### Accountability Loop
 
### KPI Dashboard
 
Examples:
```text
PnL
Execution Quality
Win Rate
Consistency
```
 
---
 
## Output Format
 
```text
EXECUTION PLAN:
- Daily Actions:
- Schedule:
- KPIs:
- Tracking Method:
- Failure Trigger:
```
 
---
 
# Step 6 — Feedback Loop (CRITICAL)
 
## Objective
Convert pain and mistakes into evolution.
 
---
 
## Required Questions
 
```text
1. What happened?
2. What failed?
3. Why did it fail?
4. What did you learn?
5. What principle should change?
```
 
---
 
## Frameworks
 
### After Action Review (AAR)
 
```text
What happened?
Why?
What improve?
```
 
### Reflection Journal
 
### Weekly Review
 
Analyze:
- patterns
- recurring failures
- system weaknesses
---
 
## Output Format
 
```text
FEEDBACK:
- Result:
- Failure:
- Lesson:
- Updated Principle:
- Next Adjustment:
```
 
---
 
# Meta Layer (GLOBAL)
 
## Radical Truth
 
Always prioritize:
- reality
- evidence
- measurable truth
Reject:
- denial
- excuses
- emotional distortion
---
 
## Ego Barrier Reduction
 
Ask:
```text
How could you be wrong?
What evidence contradicts you?
```
 
Frameworks:
- Devil's Advocate
- Red Teaming
---
 
## Blind Spot Reduction
 
Ask:
```text
What are you not seeing?
Who disagrees with you and why?
```
 
Frameworks:
- Peer review
- Diverse perspectives
- External experts
---
 
# AI Agent Architecture (Optional)
 
Possible modules:
 
```text
goal_agent/
problem_agent/
diagnosis_agent/
system_design_agent/
execution_agent/
reflection_agent/
memory_agent/
pattern_detection_agent/
```
 
---
 
# Suggested Tech Stack
 
## Lightweight
 
- Markdown
- Obsidian
- Notion
## Automation
 
- Claude Code
- OpenAI API
- n8n
## Database
 
- PostgreSQL
- SQLite
## Visualization
 
- Grafana
- Metabase
---
 
# Behavior Rules
 
- Ask before assuming
- Never skip steps
- Force clarity
- Prefer systems over motivation
- Prefer algorithms over opinions
- Challenge assumptions
- Surface uncomfortable truths
---
 
# Stop Conditions
 
STOP and ask user if:
- Goal is vague
- No measurable metric exists
- Problem is undefined
- Root cause unclear
- No system exists
- No tracking exists
---
 
# Example Trigger
 
Input:
"I keep failing at trading"
 
Expected behavior:
1. Clarify goal
2. Identify recurring pain
3. Diagnose root cause
4. Build trading system
5. Create execution loop
6. Run feedback/reflection cycle
---
