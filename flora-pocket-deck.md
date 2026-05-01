# FLORA Interview Pocket Deck

**Tue Apr 28, 3pm EDT | 60 min | HackerRank | Python | No AI**

Cards are organized by reading time. Consume in order or jump to what you need.

---

## 1-MINUTE CARDS (bite — read while waiting for coffee)

---

### CARD 1min-1 — The 60-minute time budget, cold

**0–7 min: Read everything. Ask 3 clarifying questions. Don't touch the keyboard.**
7–12 min: Write your plan in comments, narrate it.
12–35 min: Build core logic. Run tests after each function.
35–47 min: Edge cases — empty input, nulls, malformed records.
47–58 min: "At 10M records, here's what I'd change…"
58–60 min: Wrap, invite their questions.

**The phase you'll skip under pressure: the clarify phase. Don't.**

---

### CARD 1min-2 — "First 5 minutes = clarify, don't code"

Ask three specific questions before writing a line:
- "Can the input be empty? Return empty list or None?"
- "Is timestamp epoch int or ISO string?"
- "Does 'successful' mean 200 exactly or 2xx range?"

**Evaluators watch for this. It's the single biggest signal of senior thinking.**

Not "are there edge cases?" — that's too vague. Be specific.

---

### CARD 1min-3 — Python gotcha #1: json.load vs json.loads

`json.load(f)` → takes a **file object** (use inside `with open(...)`)
`json.loads(s)` → takes a **string**

```python
with open('data.json') as f:
    data = json.load(f)      # FILE
data = json.loads('{"k":1}') # STRING
```

**You've mixed these up under stress before. Say it once before you type it.**

---

### CARD 1min-4 — Python gotcha #2: collections imports

The four you'll need, know them cold:

```python
from collections import defaultdict, Counter, deque, OrderedDict
```

**`defaultdict(list)` for grouping. `deque` for sliding windows. `Counter` for frequency.**

No external libraries. Only stdlib. This is your toolbox.

---

### CARD 1min-5 — The grouping pattern (30 seconds to recall)

```python
groups = defaultdict(list)
for r in records:
    groups[r['key']].append(r)
```

**This is the skeleton of 80% of Systems-Data problems.** Memorize the shape, not the specifics.

After grouping: sort within each group, or compute an aggregate. That's it.

---

### CARD 1min-6 — The sliding window pattern (deque)

```python
window = deque()
for ts in sorted_timestamps:
    window.append(ts)
    while window and ts - window[0] > WINDOW_SIZE:
        window.popleft()
    if len(window) > THRESHOLD:
        # violation
```

**Evict from the left, grow from the right. That's the whole move.**

---

### CARD 1min-7 — Trade-off narration template

When you make a data structure choice, say this out loud:

**"I'm using [X] here — that's O(Y). If we needed real-time instead of batch, I'd switch to [Z]."**

Examples:
- sorted list → "O(n log n). Fine for batch."
- defaultdict → "O(1) lookup on insert. Better than scanning a list."
- deque → "O(1) pop from left. Lists would be O(n)."


---

### CARD 1min-8 — Run code early and often

**After every helper function, run it with a tiny test case before moving on.**

```python
# Quick sanity check before I continue
print(parse_events([{"job_id": "1", "event": "QUEUED", "ts": 100}]))
```

Don't wait for "done." Test iteratively — bugs caught at 15 min are free. Bugs caught at 50 min are fatal.

---

### CARD 1min-9 — Safe dict access (no KeyError)

```python
value = record.get('field', default_value)
nested = record.get('metadata', {}).get('width')
```

**Never index directly into a dict from unknown input.** Always `.get()` with a fallback.

If `metadata` is missing entirely, `.get('metadata', {})` returns `{}` and the second `.get('width')` returns `None` safely.


---

### CARD 1min-10 — "Check before you optimize" move

At end of core implementation, before you add error handling:

**"Before I add edge-case handling, does the basic approach look right to you?"**

This does two things: prevents polishing the wrong solution, and signals collaborative instinct — important for a senior role on a 50-person team.


---

### CARD 1min-11 — What "Systems-Data" means (two words, two signals)

**"Data"** = structured records, JSON/CSV, grouping, filtering, timestamps, missing fields.
**"Systems"** = state across records, ordering concerns, volume/scale nudges, trade-off questions.

NOT Leetcode hard. NOT trees or graphs. NOT DP.

If the problem looks like abstract algorithm work, slow down and reframe it as a data problem.


---

### CARD 1min-12 — If you're behind at the 35-min mark

**Say it out loud: "I'm going to skip error-handling for now and come back if there's time."**

Don't silently cut corners. Narrating the trade-off scores better than silent omission.

Then: finish core logic, name what's missing, move to edge cases you CAN hit.


---

### CARD 1min-13 — Sorting syntax (know cold)

```python
# Single key
sorted(items, key=lambda x: x['score'], reverse=True)

# Multi-key (group asc, score desc)
sorted(items, key=lambda x: (x['group'], -x['score']))
```

**The lambda syntax is what trips people up. Write it once in your warm-up block.**


---

### CARD 1min-14 — Python gotcha #3: sorted() returns new list, .sort() mutates

`sorted(items)` → returns a **new list**, original unchanged.
`items.sort()` → mutates in place, returns `None`.

**Common trap: `result = items.sort()` then using `result` — it's `None`.**

Use `sorted()` in almost all interview contexts unless you explicitly want mutation.

---

### CARD 1min-15 — "Why FLORA?" in one sentence

If they ask at the start:

**"I'm drawn to tools that make experts more powerful rather than replacing them — FAUNA's model-agnostic approach is the right framing for this moment. And I want to be somewhere where I'm actually building."**

Don't over-explain. Deliver it, move on. The coding is the real answer.

---

## 2-MINUTE CARDS (chunk — read during a quick stop)

---

### CARD 2min-1 — The clarifying-questions framework

Before you write a line, ask in this order:

1. **Input shape**: "What format is the input — file, list, stdin? JSON or CSV?"
2. **Output contract**: "What should I return — a dict, a list, print to stdout?"
3. **Edge case pre-flight**: "Can the input be empty? Are there duplicate IDs? Out-of-order events?"
4. **Domain ambiguity**: "Does 'successful' mean status == 200 or 2xx range? Can a job have multiple STARTED events?"
5. **Volume signal**: "You mentioned 10M records — should I optimize for memory, or is batch processing fine?"

**Don't ask all five.** Pick the three most relevant. The goal is to signal that you think before you code, not to interrogate them.

Phrase your questions as specific, binary-ish choices — not open-ended fishing. Evaluators notice the precision of your questions as much as the answers you get.

---

### CARD 2min-2 — Write your plan in comments first (the narration move)

Don't start coding immediately. Write this at the top:

```python
# Plan:
# 1. Parse input into list of dicts
# 2. Group events by job_id using defaultdict
# 3. For each job, compute duration if both QUEUED and COMPLETED present
# 4. Return average
```

Say out loud: "I'm going to write my plan first so we're aligned before I dive in."

This is not wasted time. It does three things: catches design flaws before you've sunk 20 minutes, shows the interviewer your reasoning structure, and gives you a checklist to follow when you're in the weeds.

**If your plan is wrong, you want them to redirect you at this point — not at minute 40.**


---

### CARD 2min-3 — Event pipeline pattern (state machine in a dict)

Problem type: events with job_id + status + timestamp. Compute durations.

```python
job_times = defaultdict(dict)
for event in events:
    job_times[event['job_id']][event['event_type']] = event['timestamp']

durations = []
for job_id, times in job_times.items():
    if 'QUEUED' in times and 'COMPLETED' in times:
        durations.append(times['COMPLETED'] - times['QUEUED'])

return sum(durations) / len(durations) if durations else 0
```

Clarifying questions to pre-ask: Can a job have multiple STARTED events (retries)? Are events in chronological order? Should FAILED jobs be included?

**The tricky part is retries** — if a job can restart, you need first vs. last STARTED. Ask before you code.

Scale follow-up they'll ask: "How would you handle this as a Kafka stream?" Answer: maintain a running dict in memory, emit a result when a terminal event closes a job.


---

### CARD 2min-4 — Log parsing + aggregation pattern

Problem type: filter records by condition, group, compute per-group statistic.

```python
from collections import defaultdict

def top_slow_endpoints(logs, n=5):
    endpoint_durations = defaultdict(list)
    for log in logs:
        if 200 <= log['status_code'] < 300:
            endpoint_durations[log['endpoint']].append(log['duration_ms'])
    
    averages = {ep: sum(d)/len(d) for ep, d in endpoint_durations.items()}
    return sorted(averages.items(), key=lambda x: x[1], reverse=True)[:n]
```

**Pattern: filter first, then group, then aggregate, then sort.** In that order every time.

Clarifying questions: What's "succeeded" — 200 or 2xx? Can duration_ms be null? Are logs sorted by timestamp?

The dict comprehension at the end is where people make mistakes under pressure. Write it out longhand if needed — correctness over cleverness.


---

### CARD 2min-5 — The three failure modes (and how to avoid each)

**Failure 1: Jumping to code without modeling the data.**
Fix: Before writing any code, sketch a 3-record example in a comment. Know what a "good" record looks like and what an "ugly" one looks like.

**Failure 2: Ignoring the volume number they gave you.**
Fix: When the problem says "10M records/day," acknowledge it. You don't need to rewrite everything — just say: "At 10M records, this O(n log n) sort is fine for batch, but for real-time I'd switch to a streaming approach."

**Failure 3: Treating the end of core logic as the end of the interview.**
Fix: After core logic works → edge cases → scale discussion → invite their questions. If you run out of ideas: "I'd add logging for observability and metrics on processing time per batch — those matter in a production pipeline."

**The senior move is filling that last 12 minutes.** Junior engineers stop when the tests pass.


---

### CARD 2min-6 — JSON + missing data pattern

Problem type: JSON file with creative assets, some fields missing/null. Group by a key, compute stats.

```python
import json
from collections import defaultdict

with open(filepath) as f:
    assets = json.load(f)  # FILE object — not json.loads

groups = defaultdict(lambda: {'count': 0, 'latest': None})
for asset in assets:
    model = asset.get('model_used', 'unknown')
    groups[model]['count'] += 1
    ts = asset.get('created_at')
    if ts and (groups[model]['latest'] is None or ts > groups[model]['latest']):
        groups[model]['latest'] = ts
```

**Say explicitly how you handle nulls before you code it:** "I'm going to skip records where dimensions are missing rather than treat them as 0, because a 0-area asset would skew the average. Is that right?"

This signals domain awareness, not just syntax knowledge.


---

### CARD 2min-7 — Sahar's strongest intro (30-second version)

If they ask "tell me about yourself" before the coding:

"I'm a senior engineer with a background that's been split between hands-on building and leading technical organizations. Most recently I ran Integrity Institute, which I founded — grew it into a recognized research institution working on platform safety. Before and alongside that, I've shipped real products end-to-end. I'm deliberately seeking roles where I'm actually building — not managing people who build."

---

### CARD 2min-8 — State machine / invalid transitions pattern

Problem type: workflow transitions (from_state → to_state), detect invalid ones.

```python
VALID_TRANSITIONS = {
    'QUEUED': {'STARTED', 'FAILED'},
    'STARTED': {'COMPLETED', 'FAILED'},
    'COMPLETED': set(),
    'FAILED': {'QUEUED'},  # retries allowed?
}

violations = []
current_state = {}
for event in sorted(events, key=lambda x: x['timestamp']):
    job_id = event['job_id']
    from_state = current_state.get(job_id, 'QUEUED')
    to_state = event['to_state']
    if to_state not in VALID_TRANSITIONS.get(from_state, set()):
        violations.append({'job_id': job_id, 'invalid': f"{from_state}→{to_state}"})
    current_state[job_id] = to_state
```

**Clarify before coding**: Are retries (COMPLETED → QUEUED) valid? What's the initial state for a new job?


---

### CARD 2min-9 — What to ask at the end (pick one)

If an engineer joins after the coding, use one of these:

**Best pick (shows you know the product):**
"FAUNA launched April 3rd with 50+ models. What's been the hardest engineering problem post-launch — managing parallel generation jobs at scale, or something else?"

**Tech depth pick:**
"You moved to Vercel's AI SDK and DurableAgent for FAUNA's orchestration — what drove that over something like Temporal? Was it developer velocity or something structural?"

**Role clarity pick:**
"You're hiring across backend and applied AI simultaneously — where's the biggest gap you're trying to close?"

**Don't ask about compensation. Don't ask what they do.**


---

### CARD 2min-10 — Rate-limit detection pattern

Problem type: find users who exceeded N requests in any 60-second sliding window.

```python
from collections import defaultdict, deque

def find_rate_violators(requests, limit=10, window_secs=60):
    user_timestamps = defaultdict(list)
    for req in requests:
        user_timestamps[req['user_id']].append(req['timestamp'])
    
    violators = set()
    for user_id, timestamps in user_timestamps.items():
        timestamps.sort()
        window = deque()
        for ts in timestamps:
            window.append(ts)
            while window and ts - window[0] > window_secs:
                window.popleft()
            if len(window) > limit:
                violators.add(user_id)
    return list(violators)
```

**Say out loud**: "This is essentially what you'd run in a billing or abuse detection context — I've seen this pattern in production."

Clarify: Is [t, t+60] inclusive on both ends? Should I report once per user or once per violation?

---

### CARD 2min-11 — Handling the multi-part problem

If the problem has multiple parts:

1. Ask upfront: **"How many parts are there?"** so you can pace yourself.
2. Treat each part as a mini-iteration: clarify → plan → code → test.
3. Don't try to read all parts before starting Part 1.
4. At Part 1 completion, ask: **"Does this look right before I move to Part 2?"**

If you're behind at Part 2: "I'm going to stub Part 3 with a comment explaining my approach rather than leaving it blank." Then code the core of Part 3, even if incomplete.

**Finishing three imperfect parts beats finishing one perfect part.**


---

### CARD 2min-12 — The "volume nudge" response

When the problem says "imagine 10M records/day" or "this runs every 5 minutes":

**Don't ignore it.** This is a designed signal. Respond with:

"At 10M records, my current O(n log n) sort is fine for batch processing — that's about X seconds depending on hardware. If this needed to be real-time, I'd switch to a streaming approach: process events as they arrive, maintain a rolling window in memory, and emit results when a terminal state is reached. For truly large-scale, I'd consider partitioning by user_id so the state fits in distributed memory."

You don't need to rewrite the solution. You just need to show you saw the number and reasoned about it.


---

## 5-MINUTE CARDS (study — read on a longer break)

---

### CARD 5min-1 — Full worked example: Event Pipeline (Example A)

**Prompt:** Given a list of job events with `job_id`, `status` (QUEUED/STARTED/COMPLETED/FAILED), and `timestamp` (epoch int). Return a dict mapping each `job_id` to its total processing time (STARTED to COMPLETED or STARTED to FAILED). Ignore jobs that never started or are still in progress.

**Clarifying questions to ask first (say these out loud):**
- "Can a job have multiple STARTED events — e.g., after a retry? If so, should I use the first or last STARTED?"
- "Are events guaranteed to be in chronological order, or could they arrive out of sequence?"
- "Should I return 0 for failed jobs, or exclude them?"
- "What's the expected input size? Does this need to handle millions of records or is this a batch job?"

**Comment plan:**
```python
# Plan:
# 1. Group events by job_id using defaultdict
# 2. For each job, sort events by timestamp
# 3. Find STARTED event and terminal event (COMPLETED or FAILED)
# 4. Compute delta; skip jobs without both
# 5. Return dict of job_id -> duration
```

**Implementation approach:** Group events by job_id into a dict of lists. Sort each job's events by timestamp. Walk the sorted list to find the relevant STARTED event and the terminal event. Compute the delta. The tricky part: clarify upfront whether retries mean first or last STARTED event — this changes your implementation.

**Edge cases to handle:**
- Job with STARTED but no terminal event (in progress — skip)
- Job with no STARTED at all (queued but never ran — skip)
- Events arriving out of order (sort before processing)
- Empty input (return empty dict)

**Scale follow-up (they will ask this):**
"How would you change this if events came in from a Kafka stream in real time?"
Answer: "Maintain a running dict in memory keyed by job_id. Process each event as it arrives. When a terminal event (COMPLETED/FAILED) comes in and we have a corresponding STARTED, emit the result and clean up that job's state. The tricky part is memory management — you'd need a TTL to evict jobs that never complete."


---

### CARD 5min-2 — Full worked example: Rate-Limit Detection (Example B)

**Prompt:** Given a log of API requests with `user_id`, `endpoint`, and `timestamp` (epoch seconds). Find all user+endpoint pairs that made more than 10 requests in any 60-second sliding window.

**Clarifying questions:**
- "Is 'any 60-second window' inclusive on both ends — [t, t+60] or [t, t+60)?"
- "Should I return just the user_id + endpoint, or also include the timestamp of the first violation?"
- "Are requests already sorted by timestamp, or should I sort them?"
- "Should I deduplicate — if a user violates at t=5 and t=10, report once or twice?"

**Comment plan:**
```python
# Plan:
# 1. Group requests by (user_id, endpoint) tuple using defaultdict
# 2. For each group, sort timestamps
# 3. Slide a deque window: push new ts, evict old ts, check size
# 4. When size > 10, add pair to violators set (set prevents duplicates)
# 5. Return list of violating pairs
```

**Implementation:** Group by (user_id, endpoint) pair. Sort each group by timestamp. For each group, use a deque: push each timestamp, evict entries older than 60 seconds from the left using `while window and ts - window[0] > 60`, check if window size > 10. Add violating pairs to a set (not a list) to prevent duplicate reporting.

**Complexity to mention:** O(n log n) for the sort — that's the dominant cost. The deque ops are O(1) amortized.

**Bonus context to drop:** "This is essentially what you'd run in a billing or abuse detection context."

**Edge cases:**
- User with only 1 request (no violation possible)
- All requests at the same timestamp (window size = all of them)
- Empty input


---

### CARD 5min-3 — Full worked example: Schema Transformation (Example C)

**Prompt:** JSON file of creative assets: `{id, name, model_used, tags: [], created_at, metadata: {width, height}}`. Some records have missing/null fields. For each `model_used`: count of assets, average dimensions (width × height), most recent `created_at`. Skip records with missing `model_used`.

**Clarifying questions:**
- "Should I skip records where width or height is null, or treat them as 0 in the average?"
- "`created_at` — ISO 8601 string or epoch? Can I use string comparison for 'most recent'?"
- "Should I return a dict, print it, or write to a file?"
- "Are there records where `metadata` itself is missing entirely, not just null dimensions?"

**Comment plan:**
```python
# Plan:
# 1. json.load() the file (FILE object, not string)
# 2. Skip records missing model_used
# 3. Accumulate count, dimension pairs, created_at candidates per model
# 4. Compute averages and pick max created_at
# 5. Return structured dict
```

**Key implementation notes:**

`json.load(f)` not `json.loads` — it's a file. Say this out loud before typing.

For safe nested access: `record.get('metadata', {}).get('width')` — handles missing metadata entirely.

For `created_at`: ISO 8601 sorts lexicographically, so string max() works. Say: "I'm using string comparison for max created_at — this works because ISO 8601 format sorts correctly as a string. If the format were ambiguous, I'd parse it with `datetime.fromisoformat`."

**Null handling (say this explicitly before coding it):**
"I'm going to skip records where width or height is missing rather than treat them as 0, because a 0-area asset would skew the average. Is that the right call?"

This is worth 30 seconds of conversation. The answer tells you what they care about.

**Edge cases:**
- Record where `metadata` key is entirely absent (not just null)
- All records skipped (no valid model_used) — return empty dict
- Only one asset per model (average = that one value)


---

### CARD 5min-4 — The 60-minute screen, phase by phase

A walkthrough of how to spend every minute.

**0–7 min: Read + clarify**
Read the entire problem before asking anything. Then ask 3 specific questions. Don't ask "are there edge cases?" — ask specific binary questions (see 1min-2). Write the clarifying questions in a comment so the interviewer sees your reasoning.

**7–12 min: Sketch your plan**
Write a comment block: `# Plan: 1. ... 2. ... 3. ...` Say it out loud as you write. This is not waste — it's a demonstration. It catches design flaws before you've sunk 20 minutes into the wrong approach.

**12–35 min: Core implementation**
Build the main logic. Run after each helper function with a tiny test case — don't wait for "done." If you realize your plan was wrong, say it out loud: "I'm going to adjust my approach here — I hadn't accounted for out-of-order events." Pivoting out loud is fine. Silent wrong turns are not.

**35–47 min: Edge cases + cleanup**
Handle malformed input, empty cases, missing keys. Use `.get()` with defaults. Add the safety checks you told them about in clarification. If you said "I'll skip nulls" — now write the code that actually skips them.

**47–58 min: Optimize + discuss**
Talk about complexity: "This is O(n log n) because of the sort. At 10M records that's fine for batch." Talk about what you'd change for real-time. Mention observability: "In production I'd add logging and metrics on processing time per batch."

**58–60 min: Wrap**
One-sentence summary. "The core pipeline handles X. I'd add Y and Z before shipping." Thank them. If they're present: one of your prepared questions (see 2min-9).

**The senior signal in every phase:** Make your reasoning audible. Evaluators can't score what they can't see.

---

### CARD 5min-5 — Sahar's stories, compressed (STAR format)

**Story 1 — Integrity Institute (building at scale under uncertainty)**
Situation: No existing org for platform integrity research. Task: Build something that could influence how platforms handle harm. Action: Founded II, grew it from zero — hiring, agenda, partnerships with platforms and policymakers. Result: Recognized institution with policy influence.
One-liner: "I built something from nothing, in a domain where the playbook didn't exist yet."

**Story 2 — Full-stack shipping (hands-on builder)**
Situation: Engineering work across career. Task: Ship real products end-to-end. Action: Wrote the code, not just the specs.
One-liner: "I'm not a big-company bureaucrat — I write the code."

**Story 3 — Non-technical stakeholders (honored voice frame)**
Situation: Technical decisions with organizational/political complexity. Task: Influence outcome without final authority. Action: Translated between technical reality and org needs, pushed back thoughtfully. Result: Influenced decisions without burning relationships.
One-liner: "I know how to be the technical voice in a room that's not all technical."

**Story 6 — Small team / startup**
Situation: Early-stage org, few people, consequential decisions fast. Task: Move without over-process. Action: Shipped, iterated, wore multiple hats. Result: Things got built.
One-liner: "I'm comfortable at the stage where there's no playbook and shipping is the strategy."


---

### CARD 5min-6 — Python warm-up block (do this Tue 10–11:30am)

Run these two problems from scratch before the 3pm screen. Time yourself.

**Problem A (Event Pipeline):**
Write `avg_completion_time(events)` — takes a list of `{job_id, event_type, timestamp}` dicts. Returns average time from QUEUED to COMPLETED for jobs with both events. Do it in under 15 minutes, including a manual test with 3-4 records.

**Problem B (Rate Limit Detection):**
Write `find_rate_violators(requests, limit=10, window_secs=60)` — takes a list of `{user_id, timestamp}` dicts. Returns list of user_ids who violated. Do it in under 15 minutes.

**Check yourself on every gotcha:**
- Did you use `json.load(f)` not `json.loads` for file reading?
- Did you import from collections at the top?
- Did you use `.get()` for dict access, not direct indexing?
- Did your sort lambda work without a typo?
- Did you run a test case after each function?

**If either problem takes you more than 20 min:** you need another rep tonight. The warm-up block is not optional.

The goal is your hands remembering the patterns, not your brain learning them for the first time at 3pm.


---

### CARD 5min-7 — The end-of-interview playbook (last 12 minutes)

Most candidates stop when the tests pass. Don't.

**After core logic is working:**

Step 1 — Edge cases (5 min):
- Empty input: `if not records: return {}`
- Missing keys: `.get('field', default)` everywhere
- Malformed records: skip with a comment — "In production I'd log this rather than silently drop it"
- Duplicate records: do you need to deduplicate?

Step 2 — Scale discussion (3 min):
Acknowledge the volume signal. "At 10M records, the current approach is O(n log n) — that's acceptable for a batch job. For real-time I'd stream events through a rolling window and emit results as terminal events arrive. For distributed scale, partition by job_id so each node owns a subset."

Step 3 — Observability (2 min):
"In a production version I'd add: processing time metrics per batch, a count of skipped malformed records, and an alert if the error rate exceeds X%." This sounds like something a senior engineer thinks about — because it is.

Step 4 — Invite their questions (2 min):
"I think that covers the main cases. Are there aspects of the approach you'd like to dig into?" Then — if they're present — one of your prepared questions (see 2min-9 card).

**The senior signal:** You filled the time with substance. You didn't stop when the tests passed.

---

### CARD 5min-8 — What the screen is actually testing (and what it's not)

**What it IS testing:**
1. Can you structure a solution before you code? (the plan-in-comments move)
2. Can you ask specific, useful clarifying questions? (not "any edge cases?")
3. Do you test iteratively?
4. Do you reason about trade-offs out loud? (O(n log n) for batch vs. streaming for real-time)
5. Do you handle missing/malformed data gracefully? (.get() everywhere)

**What it is NOT testing:**
- Whether you know the optimal algorithm for a DSA problem
- Whether you can solve LeetCode Hard under pressure
- Whether you've memorized every Python method

**The mindset shift:** You are not taking a test. You are demonstrating how you think on a real problem, with a human watching. The human can see your screen and your comments. Every comment is content. Every narrated trade-off is a score.

**If you get stuck:** Say it out loud. "I'm deciding between a dict and a list here — the dict is O(1) lookup but uses more memory. Given that the input could be large, I'll go with the dict." Thinking out loud when stuck is MORE impressive than never seeming stuck. Senior engineers get stuck. They narrate through it.

**The senior bar:** Ship good code AND explain your decisions to a junior engineer. Prove you can do both at the same time.

---
