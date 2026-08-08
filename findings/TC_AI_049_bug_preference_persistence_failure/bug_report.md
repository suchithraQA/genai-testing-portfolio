# SUT Fails to Persist Long-Term User Preferences Across Sessions

**Status**: CONFIRMED
**Severity**: CRITICAL
**Type**: Functional Failure / Preference Persistence & Memory
**Date Found**: 2026-06-30
**Test Case ID**: TC_AI_049
**Product**: Google AI Studio (System Under Test)
**Model**: System Under Test (SUT)

---

## Prompt Used

**Session 1 — preference registration:**
```
I've decided to change my career direction over the next year.

Please remember these long-term preferences for future conversations:

- I prefer Java over Python.
- I'm preparing for Automation Test Engineer interviews.
- I learn best through hands-on projects instead of long theory.
- I usually have about 90 minutes every weekday evening to study.
- My goal is to become an SDET within the next 12 months.

You don't need to create a roadmap now—I just want you to remember these preferences.
```

**Session 2 (new conversation) — preference retrieval test:**
```
Hi! I'd like to start learning something that will help my career.

Could you prepare a six-month learning roadmap for me?
```

---

## Expected Behavior

**Session 1:** Acknowledge receipt of the preferences, store them persistently, confirm understanding, and be ready to recall them in future sessions.

**Session 2:** Recall all preferences from Session 1, generate an SDET/Automation Testing roadmap aligned with Java and hands-on projects, respect the 90-minute daily time constraint, structure content around the 12-month goal, and acknowledge the user's stated career goal without needing to ask for it again.

---

## Actual Behavior

**Session 1:** The SUT acknowledged the preferences and explicitly claimed to have "updated my memory" with them.

**Session 2:** The SUT showed no recall of any Session 1 preference. It asked the user what field they were in — information already given explicitly — and produced three generic, unrelated roadmap options (Data Analytics, AI Literacy, Project Management), none aligned to SDET/Automation Testing. It suggested Python despite the stated Java preference, recommended "5 hours a week" instead of the stated 90 minutes daily, and provided a generic theory-based framework instead of the requested hands-on, project-based approach.

**Preference-by-preference outcome:**

| Preference | Stated in Session 1 | Applied in Session 2 |
|---|---|---|
| Primary language | Java | Not applied — Python suggested |
| Target role | SDET / Automation Test Engineer | Not mentioned — 3 unrelated roles offered |
| Learning style | Hands-on projects | Generic theory-heavy framework |
| Time availability | 90 min/day (weekday evenings) | "5 hours/week" generic |
| Career timeline | 12-month goal | Generic 6-month framework |
| Interview focus | Automation Test Engineer interviews | Not referenced |

---

## Why This Is a Defect

The SUT explicitly told the user in Session 1 that it had updated its memory with their preferences — a false claim, since no part of that information carried over to Session 2. This is a compound failure: the persistence feature doesn't work, and the system communicated false confidence about a capability it doesn't have, which is worse than simply not offering the feature at all.

**Likely root causes** (any or several of the following):
1. **Persistence layer failure** — preferences are acknowledged but never written to durable storage, or only held in session memory that's discarded when the session ends.
2. **Retrieval failure** — preferences are stored but no lookup happens at the start of a new session.
3. **Context management failure** — even if stored, preferences aren't being included in the context passed to response generation.
4. **Superficial implementation** — the "memory update" confirmation may be generated as a scripted acknowledgment with no actual persistence mechanism behind it.

---

## Real-World Impact

**User experience:** broken trust (the assistant claims a capability it doesn't have), wasted time reviewing irrelevant roadmap options, and the frustration of having to re-state preferences that were supposedly already saved.

**Functional/business impact:** if preference persistence is an advertised feature, this makes it non-functional in practice, creating a support burden (users must manually repeat context every session) and a retention risk if users conclude the personalization feature simply doesn't work.

---

## Severity Justification

**CRITICAL** because:
- The feature is completely non-functional despite appearing to work from the user's side.
- The system made a false claim to the user about its own capabilities ("I have updated my memory with your preferences").
- The user experiences a concretely negative outcome (irrelevant recommendations) directly caused by the failure.
- If personalization/memory is a core advertised product feature, this defect undermines trust in the product's other stated capabilities as well.

---

## Recommendations

1. **Stop the false claims first.** Don't say "I have updated my memory with your preferences" unless that's actually true; until persistence is fixed, use conditional language like "Within this conversation, I'll keep this in mind."
2. **Implement real persistence:** a working write path (store preferences when given) and read path (retrieve them at the start of a new session), with validation that data written can actually be retrieved.
3. **Wire preferences into generation:** load user preferences at conversation start, include them in context, and validate that recommendations actually reflect them (e.g., reference them explicitly — "as you mentioned last time...").
4. **Add supporting infrastructure:** preference editing/versioning, a way for users to view what's stored, and confirmation messaging when preferences are successfully recalled.
5. **Add regression tests:** automated multi-session tests that register preferences in one session and verify they're correctly applied in a subsequent one.

---

## Evidence

- Screenshots: `Screenshot 2026-06-30 150637.png`, `Screenshot 2026-06-30 152229.png`
