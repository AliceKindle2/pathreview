## Week 7 — Issue selection

**Issue link:** [https://github.com/ascherj/pathreview/issues/156]

**Issue title:** [README scorer test fixture is too short for its own word-count assertion]

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
I ran the test suite against the actual implementation to confirm the issue rather than guess, and found a concrete, reproducible bug: ReadmeScorer._score_readme() in agent/tools/readme_scorer.py (used by the Agent System when orchestrating a portfolio review) undercounts words for realistic README content. In the test_readme_with_all_quality_signals test case — a README with installation, usage, tech-stack, badges, and a demo link — the scorer returns a word_count of only 51, well under the 100-word threshold, so word_count_category comes back as "minimal" instead of the expected "comprehensive", even though every other quality signal (installation, usage, badges, demo, tech stack) is correctly detected. Because word_count_category and the length-based bonus in overall_score both key off this undercounted value, well-documented READMEs can be misclassified as sparse, which would push PathReview's generated feedback to unfairly flag good documentation as thin. A successful fix would correct the word-counting logic (likely how content.split() treats markdown syntax, code fences, or list markers) so word counts reflect genuine prose length, bringing test_readme_with_all_quality_signals and any other length-dependent assertions back in line with the test suite's expectations.

**Branch name:** [#156-README-scorer-test]

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger