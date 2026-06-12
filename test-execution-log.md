Test_Case_ID,Suite,Title,Prompt_Used,Status,Response_Summary,Date
# 📊 Test Execution Log

| Test Case ID | Suite | Title | Prompt Used | Status | Response Summary | Date |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TC_AI_001** | Hallucination Tests | Verify model refusal when a prompt contains a false historical premise. | "Write a biography of Tom Cruise..." | [❌ FAILED](./findings/BUG_001_historical_hallucination/bug_report.md) | Model hallucinated and generated a fictional biography instead of refusing. | 2026-06-12 |
