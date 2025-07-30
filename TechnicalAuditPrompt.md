
This document is what I'd call a "Technical Audit Report" or "Code Health Assessment". It's a systematic analysis that combines:
Design Intent vs. Implementation Gap Analysis
Cross-Version Behavioral Divergence Report
Bug Triage with Impact Classification
Production Readiness Assessment
How to Prompt for Similar Documents:
Generic Template:
For Your Specific Case:
Key Phrases That Trigger This Style:
"Technical audit"
"Design vs. implementation analysis"
"Cross-version behavioral comparison"
"Production readiness assessment"
"Bug triage by severity"
"What's supposed to happen vs. what actually happens"
The format works well because it's diagnostic (finds problems), comparative (shows differences), and actionable (prioritized fix list).
"Create a technical audit of [CODEBASE/FEATURE] that:
1. Explains what each component is SUPPOSED to do (from docs/specs)
2. Shows how it's ACTUALLY implemented in the code
3. Identifies bugs, missing features, and behavioral differences between versions
4. Classifies issues by severity (🟥 Critical, 🟧 Medium, 🟨 Minor)
5. Provides a TL;DR production readiness verdict
6. Includes quick-fix recommendations

Compare these artifacts: [list files/versions]
Focus on: [specific subsystem/functionality]"
"Analyze the exit logic systems in my trading strategy. I have design docs and two code versions that behave differently. Create a technical audit that:

- Explains each exit mechanism in plain English
- Shows the actual implementation vs. intended behavior  
- Flags any non-functional features or missing variable assignments
- Explains why the two versions behave differently
- Gives an overall 'is this production ready?' assessment
- Prioritizes the most critical bugs to fix first

Files to compare: [docs.md, main.pine, copy.pine]"