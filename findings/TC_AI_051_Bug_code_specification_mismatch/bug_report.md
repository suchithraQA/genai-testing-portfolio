# Code Generation Failure: Ignores Shorthand Language Specification (Pytest vs Java)

**Status**: CONFIRMED  
**Severity**: HIGH  
**Type**: Instruction Following Failure / Specification Mismatch  
**Date Found**: 06/30/2026  
**Test Case ID**: TC_AI_051  
**Product**: Google AI Studio (Gemini)  
**Model**: Gemini 3 Flash Preview  

---

## Prompt Used

```
Generate Selenium script to click a button with ID 'submitBtn' and verify page 
title is 'Success Page'. Shorthand: Use Java, Pytest conventions, minimal 
comments. Return production-ready code.
```

---

## Expected Behavior

Given the specification "Use Java, Pytest conventions, minimal comments," Gemini should:
- Generate Java code (explicitly requested)
- BUT apply Pytest testing conventions (Python testing framework conventions)
- This is contradictory, so Gemini should:
  1. Recognize the conflict (Java language + Pytest conventions = mismatch)
  2. Ask for clarification: "Did you mean Python with Pytest, or Java with JUnit?"
  3. OR default to Java with Java testing conventions (JUnit) since that's the language specified

---

## Actual Behavior

Gemini generated **Java code using JUnit**, NOT Pytest:

```java
import org.junit.jupiter.api.*;  // ← JUnit, not Pytest
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;

public class FormTest {
    @BeforeEach  // ← JUnit annotation
    void setup() { ... }
    
    @Test  // ← JUnit annotation
    void testSubmitAndVerifyTitle() { ... }
    
    @AfterEach  // ← JUnit annotation
    void tearDown() { ... }
}
```

**What's wrong:**
- ✅ Used Java (correct)
- ❌ Used JUnit conventions (NOT Pytest)
- ❌ Did NOT acknowledge the Pytest specification
- ❌ Did NOT ask for clarification on the contradiction
- ❌ Silently overrode user specification

---

## Verification

**Why This Is A Defect:**

1. **Specification Ignored**
   - User explicitly requested "Pytest conventions"
   - Gemini generated JUnit instead
   - No acknowledgment of the specification

2. **Contradictory Requirements Not Resolved**
   - "Java" and "Pytest" are contradictory
   - Java + Pytest doesn't make sense together
   - Gemini should have asked: "Did you mean Python with Pytest?"
   - Instead, silently defaulted to JUnit

3. **Instruction Following Failed**
   - User said "Pytest conventions"
   - Gemini did something else entirely
   - This is a clear instruction following failure

4. **Silent Substitution**
   - Gemini substituted JUnit for Pytest without explanation
   - User might not notice the mismatch
   - Code won't align with team's testing conventions if they expected Pytest

---

## Real-World Impact

**Scenario 1 — Team Mismatch:**
- Developer requests "Java with Pytest conventions"
- Receives JUnit code instead
- Copies code into Python/Pytest project
- Code doesn't run (Java in Python project)
- Wastes developer time debugging

**Scenario 2 — Convention Violation:**
- Team standardized on Pytest for all testing
- Developer uses Gemini-generated JUnit code
- Code breaks team's testing convention consistency
- Other developers confused by different testing pattern

**Scenario 3 — Instruction Not Followed:**
- User has specific reason for "Pytest conventions" request
- Gemini silently ignored it
- User gets code that doesn't meet their needs
- Reduces trust in AI code generation

---

## Why Severity Is HIGH

**Reasoning:**
- **Instruction following failed**: Core requirement ignored
- **Contradictory specs not resolved**: Should have asked for clarification
- **Silent substitution**: Replaced user's specification with own choice
- **Code incompatibility**: Generated code may not work in intended context
- **Not a hallucination**: Code is correct Java/JUnit, just not what was asked for

This is HIGH because:
- Instruction following is fundamental
- Code generation should honor specifications
- Silent substitution is worse than refusal

---

## Root Cause Analysis

**Why Did This Happen?**

1. **Specification conflict recognition failed**
   - "Java" + "Pytest" = contradiction
   - Model should detect this and ask
   - Instead, picked one specification (Java) and ignored the other (Pytest)

2. **No clarification request**
   - Model has capability to ask: "I notice you requested Java with Pytest conventions. Did you mean..."
   - Instead, silently defaulted behavior

3. **Assumption made**
   - Assumed Java developers use JUnit
   - This is true, but doesn't override explicit specification
   - User explicitly asked for Pytest conventions for a reason
