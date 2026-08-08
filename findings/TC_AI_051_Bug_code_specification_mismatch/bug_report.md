# SUT Ignores Shorthand Language Specification (Pytest vs Java/JUnit)

**Status**: CONFIRMED
**Severity**: HIGH
**Type**: Instruction Following Failure / Specification Mismatch
**Date Found**: 2026-06-30
**Test Case ID**: TC_AI_051
**Product**: Google AI Studio (System Under Test)
**Model**: System Under Test (SUT)

---

## Prompt Used

```
Generate Selenium script to click a button with ID 'submitBtn' and verify page
title is 'Success Page'. Shorthand: Use Java, Pytest conventions, minimal
comments. Return production-ready code.
```

---

## Expected Behavior

The specification "Use Java, Pytest conventions" is internally contradictory — Pytest is a Python testing framework, so "Java + Pytest conventions" doesn't resolve cleanly. Given that, the SUT should either:
1. Recognize the conflict and ask for clarification (e.g., "Did you mean Python with Pytest, or Java with JUnit?"), or
2. Default to Java with Java-native testing conventions (JUnit) *and explicitly flag* that it did so because "Pytest" doesn't apply to Java.

What it should not do is silently pick one part of the specification and drop the other without any acknowledgment.

---

## Actual Behavior

The SUT generated Java code using JUnit annotations and structure (`@BeforeEach`, `@Test`, `@AfterEach` from `org.junit.jupiter.api`) — correct Java, but built on JUnit conventions rather than Pytest. It did not acknowledge the Pytest specification at all, did not flag the contradiction, and did not ask for clarification — it silently overrode part of the user's stated requirement.

---

## Why This Is a Defect

The user explicitly requested "Pytest conventions" as part of the specification, and the SUT substituted JUnit without any acknowledgment. This is a straightforward instruction-following failure compounded by silent substitution: rather than surfacing the Java/Pytest contradiction to the user, the SUT resolved it unilaterally and gave no indication that anything in the original request had been dropped or reinterpreted. Silent substitution is worse than asking for clarification or even declining, because the user may not notice the mismatch until the code fails to integrate with their actual project.

---

## Real-World Impact

**Scenario 1 — Team mismatch:** a developer requests "Java with Pytest conventions," receives JUnit code instead, and if they're working in a genuinely Python/Pytest-based project, the code doesn't run at all — wasting time debugging what looks like a straightforward integration.

**Scenario 2 — Convention violation:** a team standardized on a specific testing convention receives code that doesn't match it, creating inconsistency across the codebase and confusing other developers who expect the established pattern.

**Scenario 3 — Unmet intent:** the user likely had a specific reason for requesting Pytest conventions; because the SUT silently ignored it, they get code that technically runs but doesn't meet their actual need, reducing trust in AI-assisted code generation.

---

## Severity Justification

**HIGH** because:
- Instruction following is a fundamental expectation for code-generation tasks — a core part of the specification was dropped.
- The contradictory specification should have triggered a clarification request; instead it was resolved silently.
- The generated code, while technically correct Java/JUnit, may be incompatible with the context it was actually needed for.
- This is not a hallucination (the code itself is valid) — it's a failure to honor an explicit, if imperfectly phrased, specification.

---

## Recommendations

1. Detect contradictory specifications (e.g., a language paired with a testing framework native to a different language) before generating code.
2. When a contradiction is detected, ask for clarification rather than silently resolving it.
3. If defaulting to one interpretation is necessary, explicitly flag the substitution in the response (e.g., "Note: Pytest is a Python framework; since you asked for Java, I've used JUnit conventions instead — let me know if you meant Python").
4. Avoid assuming what a user "probably meant" (e.g., "Java developers use JUnit") as a substitute for surfacing an explicit conflict in their stated requirements.

---

## Evidence

- Screenshot: `Screenshot 2026-06-30 160300.png`
