# Response Consistency Failure: Identical Prompts With Different Whitespace Produce Different Outputs

**Status**: CONFIRMED
**Severity**: HIGH
**Type**: Consistency / Reproducibility Failure
**Date Found**: 2026-06-30
**Test Case ID**: TC_AI_050
**Product**: Google AI Studio (System Under Test)
**Model**: System Under Test (SUT)
**Temperature**: 0.1 (consistency validation)

---

## Prompt Used

**Test 1 — single paragraph, no line breaks:**
```
Define API endpoint in software development context with structure, how it works,
why it matters, real-world analogy, and importance.
```

**Test 2 — same content, multi-line/bulleted formatting:**
```
In software development, an API endpoint is...

Define:
- Structure
- How it works
- Real-world analogy
- Why it matters
```

---

## Expected Behavior

When sent semantically identical prompts that differ only in whitespace formatting (line breaks, indentation, spacing), the SUT should produce identical or near-identical responses. Whitespace is cosmetic, not semantic, and at Temperature 0.1 consistency should be very high (±5% variance).

---

## Actual Behavior

Both prompts asked for the same content (a definition, structure breakdown, HTTP methods, a real-world analogy, and importance factors), and both responses covered the same substantive ground — but with measurable differences:

- Test 1 response: ~450 words, direct and organized.
- Test 2 response: ~500 words, more verbose, with slightly different organization and slightly different examples in places.

**Variance observed:** roughly 10–15% difference in length and structure despite an identical underlying content request.

---

## Why This Is a Defect

Whitespace formatting should not influence content generation — the model should be parsing meaning, not reacting to incidental formatting. At Temperature 0.1, near-identical output is expected, but instead the same request produced meaningfully different length and structure depending on formatting alone, which compromises reproducibility: identical requests should yield identical (or near-identical) answers regardless of how they're formatted.

---

## Real-World Impact

**Scenario 1 — API integration:** a developer sending semantically identical requests with different formatting gets inconsistent responses, which is problematic for any system expecting deterministic or near-deterministic API behavior.

**Scenario 2 — Test flakiness:** QA test suites that vary prompt formatting see inconsistent outputs, making failures hard to reproduce and debug.

**Scenario 3 — User confusion:** a user who phrases the same question two different ways gets different-length responses, which is confusing and reduces trust in the system's consistency.

---

## Severity Justification

**HIGH, not CRITICAL**, because this is not a safety issue and core functionality still works — the content generated is still correct and relevant, just inconsistent in length/structure. It's rated HIGH rather than lower because consistency at Temperature 0.1 is a reasonable production expectation, and unpredictable output length undermines reliability for both automated testing and production API use.

---

## Recommendations

1. Investigate whether formatting tokens (line breaks, bullets) are influencing the model's response length/structure disproportionately to their semantic weight.
2. Add automated consistency tests that vary only whitespace/formatting across otherwise-identical prompts and assert output similarity within a defined tolerance.
3. Document expected consistency tolerances at low temperatures so this can be tracked as a measurable regression rather than an anecdotal one.

---

## Evidence

- Screenshot: `Screenshot 2026-06-30 153604.png`
