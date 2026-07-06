# Response Consistency Failure: Identical Prompts With Different Whitespace Produce Different Outputs

**Status**: CONFIRMED  
**Severity**: HIGH  
**Type**: Consistency / Reproducibility Failure  
**Date Found**: 06/30/2026  
**Test Case ID**: TC_AI_050  
**Product**: Google AI Studio (Gemini)  
**Model**: Gemini 3 Flash Preview  
**Temperature**: 0.1 (consistency validation)  

---

## Expected Behavior

When sent identical prompts with only whitespace formatting differences (line breaks, indentation, spacing), Gemini should:
- Produce identical or nearly identical responses
- Whitespace should NOT affect content generation
- At Temperature 0.1, consistency should be very high (±5% variance acceptable)

---

## Actual Behavior

**Prompt Test 1 — Single paragraph (no line breaks):**
```
Define API endpoint in software development context with structure, how it works, 
why it matters, real-world analogy, and importance.
```

**Response 1:**
- Concise definition (2 sentences)
- Structure breakdown: base URL, endpoint, method
- HTTP methods (GET, POST, PUT, DELETE)
- Restaurant analogy
- 4 importance factors
- **Length**: ~450 words
- **Structure**: Direct and organized

**Prompt Test 2 — Multi-line format with whitespace (identical content):**
```
In software development, an API endpoint is...

Define:
- Structure
- How it works
- Real-world analogy
- Why it matters
```

**Response 2:**
- Same definition
- Same structure breakdown
- Same HTTP methods
- Same restaurant analogy
- Same importance factors
- **Length**: ~500 words
- **Structure**: More verbose, slightly different organization
- **Content additions**: Slightly different examples in some sections

**Variance observed:** ~10-15% difference in length and structure despite identical content request

---

## Verification

**Why This Is A Defect:**

1. **Whitespace Should Not Affect Content**
   - Formatting is cosmetic, not semantic
   - Model should parse meaning, not react to whitespace
   - At Temperature 0.1, responses should be near-identical

2. **Reproducibility Compromised**
   - Same prompt, different formatting = different output
   - Makes testing unreliable
   - Users can't consistently get same answers

3. **Quality Inconsistency**
   - Sometimes verbose (500 words)
   - Sometimes concise (450 words)
   - User can't control output style reliably

4. **For Production Use, This Is Critical**
   - API users expect consistent responses
   - Whitespace variations shouldn't change output
   - Creates unpredictable behavior

---

## Real-World Impact

**Scenario 1 — API Integration:**
- Developer sends identical requests to Gemini API with different formatting
- Gets slightly different responses each time
- API consumers expect consistency
- System becomes unreliable

**Scenario 2 — Test Flakiness:**
- QA tests prompt formatting variations
- Gets different outputs
- Tests fail inconsistently
- Difficult to debug

**Scenario 3 — User Confusion:**
- User sends same question two ways (different whitespace)
- Gets different length responses
- Confusing and unpredictable
- Reduces trust in AI consistency

---

## Why Severity Is HIGH (Not Critical)

**Reasoning:**
- **Not a safety issue**: No harmful content generated
- **Not a major feature failure**: Core functionality works
- **But is a quality issue**: Consistency is compromised
- **Impacts reliability**: Makes output unpredictable
- **Reproducibility affected**: Tests can't rely on consistent output

This is HIGH because:
- Consistency is expected at Temperature 0.1
- Whitespace should never affect responses
- Production use requires reliable outputs
