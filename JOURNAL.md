## Week 7 — Issue selection

**Issue link:** [https://github.com/ascherj/pathreview/issues/156]

**Issue title:** [README scorer test fixture is too short for its own word-count assertion]

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
I ran the test suite against the actual implementation to confirm the issue rather than guess, and found a concrete, reproducible bug: ReadmeScorer._score_readme() in agent/tools/readme_scorer.py (used by the Agent System when orchestrating a portfolio review) undercounts words for realistic README content. In the test_readme_with_all_quality_signals test case — a README with installation, usage, tech-stack, badges, and a demo link — the scorer returns a word_count of only 51, well under the 100-word threshold, so word_count_category comes back as "minimal" instead of the expected "comprehensive", even though every other quality signal (installation, usage, badges, demo, tech stack) is correctly detected. Because word_count_category and the length-based bonus in overall_score both key off this undercounted value, well-documented READMEs can be misclassified as sparse, which would push PathReview's generated feedback to unfairly flag good documentation as thin. A successful fix would correct the word-counting logic (likely how content.split() treats markdown syntax, code fences, or list markers) so word counts reflect genuine prose length, bringing test_readme_with_all_quality_signals and any other length-dependent assertions back in line with the test suite's expectations.

**Branch name:** [#156-README-scorer-test]

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [https://github.com/AliceKindle2/pathreview/blob/fix/%23156-README-scorer-test/tests/unit/test_readme_scorer.py]

**Reproduction summary:**
Ran ReadmeScorer._score_readme() directly against the fixture in test_readme_with_all_quality_signals and found the actual word count is 51, not >100 as the test asserts — the scorer's word-splitting logic is correct, but the test fixture's markdown structure (headings, code fences, bullets, badges) inflates its apparent length without adding enough real prose to cross the threshold.

**PLAN.md link:** [https://github.com/AliceKindle2/pathreview/blob/fix/%23156-README-scorer-test/PLAN.md]

**Walkthrough video (recommended):** [link to your Loom video, ≤2 min — shared for early feedback]

**Blockers or open questions:**
* Still need to confirm no other test fixtures in test_readme_scorer.py (or elsewhere in the repo) share this same boundary-mismatch pattern before considering the fix complete.
* Haven't yet verified that expanded fixture text won't accidentally trip other regex-based assertions (installation/usage/tech-stack detection) once prose is added.