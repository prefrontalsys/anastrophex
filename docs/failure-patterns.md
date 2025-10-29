# Documented Failure Patterns

Real failure patterns observed during AI debugging sessions, with triggers and effective interventions.

**Research foundation:** These patterns exemplify executive dysfunction and impulsivity documented in LLM research. See [bibliography.md](./bibliography.md) for academic citations.

**Key research validations:**
- **Loop detection:** "Repetitive action sequences serve as a powerful signal of an agent being stuck in an infinite loop" (Agentic Metacognition, arXiv:2509.19783)
- **Impulsivity:** LLMs exhibit "striking collapse" as problems get harder - they reduce reasoning effort rather than thinking more (Apple Research via MIT News, 2024)
- **Poor planning:** LLMs show "poor planning abilities identified as a particular weakness" on executive function tasks (Vazquez, 2023)

## Pattern: Black Formatting Loop

**Observed:** Oct 23, 2025 - crewai-test CI/CD debugging

**Research parallel:** This exemplifies **impulsivity** (rushing instead of deliberating) and **poor planning** (not identifying the right tool/strategy). Apple Research found LLMs "reduce reasoning effort rather than thinking more" when problems get harder (MIT News, 2024).

### Behavior
1. CI fails with "Black formatting check failed"
2. Claude reads error: "would reformat file.py"
3. Claude manually edits whitespace/blank lines (attempt #1)
4. Push, CI fails again
5. Claude edits different whitespace (attempt #2)
6. Push, CI fails again
7. Claude edits more formatting (attempt #3)
8. **Loop continues 5+ commits**

### Root Cause
- Not using the formatter tool directly
- Guessing what Black wants instead of asking Black
- Treating symptom (formatting errors) not disease (not using the tool)
- **Research context:** Vazquez (2023) found "poor planning abilities" in GPT-3.5 when tested on executive function tasks

### Trigger Conditions
```
IF tool_calls.count("Edit") >= 3
   AND file_path.endswith(".py")
   AND recent_CI_failure.contains("black")
   AND NOT tool_calls.contains("black")
THEN: Loop detected
```

### Effective Intervention
**Timing:** After 2 manual edits, before attempt #3

**Reminder:**
```
⚠️ Loop detected: You've edited formatting 2 times manually.

CLAUDE.md says: "NEVER guess what formatters want - use the actual tool"

Try: black file.py
Then: git diff to see what changed
Then: Commit those exact changes
```

**Effectiveness:** User intervention required (broke the loop when pointed out)

### Learning
- The tool (`black file.py`) fixes it in one command
- Manual edits waste time and don't address the root cause
- Web search confirmed: Black has minimal config, just run it

---

## Pattern: Mypy Strict Config Mismatch

**Observed:** Oct 23, 2025 - crewai-test CI/CD debugging

### Behavior
1. CI fails with 77 mypy type errors
2. Errors all from external library: "module has no attribute X"
3. Claude reads config: `disallow_untyped_defs = true`, `disallow_any_generics = true`
4. This is copy-pasted from production repo (mnemex)
5. But crewai-test is experimental, using CrewAI without type stubs

### Root Cause
- CI strictness doesn't match project type
- Copy-paste config from production → test project
- External dependency (CrewAI) has no type stubs

### Trigger Conditions
```
IF mypy_errors.count() > 50
   AND error_messages.contains("missing library stubs")
   AND error_messages.contains("import-untyped")
   AND config.contains("strict = true" OR "disallow_any_unimported")
THEN: Config mismatch likely
```

### Effective Intervention
**Timing:** After seeing 77 errors, before trying to fix each one

**Reminder:**
```
💡 High error count from external library suggests config mismatch.

CLAUDE.md CI/CD section: "Match strictness to project type"

Project types:
- Production package → strict = true
- Test/experimental → ignore_missing_imports = true
- Prototype → skip mypy or continue-on-error

What type is this project?
```

**Effectiveness:** Not tested yet (no reminder was given)

### Solution Applied
```toml
[tool.mypy]
ignore_missing_imports = true
disable_error_code = ["attr-defined", "var-annotated", "index"]
```

Result: 77 errors → 0 errors

---

## Pattern: Missing Dependency Misdiagnosis

**Observed:** Oct 23, 2025 - crewai-test CI/CD debugging

### Behavior
1. CI fails: `pytest: error: unrecognized arguments: --cov=crewai_test --cov-report=xml`
2. Error message literally says "unrecognized arguments"
3. But pattern suggests looking at pytest command, not dependencies

### Root Cause
- CI workflow uses `pytest --cov`
- Package doesn't have `pytest-cov` in dev dependencies
- Error message is clear, but easy to misinterpret as "wrong CI config"

### Trigger Conditions
```
IF error_message.contains("unrecognized arguments: --cov")
   AND NOT grep("pytest-cov", "pyproject.toml")
THEN: Missing dependency
```

### Effective Intervention
**Timing:** Immediately after seeing the error

**Reminder:**
```
📋 Error says "unrecognized arguments: --cov"

This usually means pytest-cov is not installed.

Check: grep -i "pytest-cov" pyproject.toml
Fix: Add pytest-cov>=4.0.0 to dev dependencies
```

**Effectiveness:** Would have saved immediate diagnosis

### Learning
- Error messages are often literal - "unrecognized arguments" = missing plugin
- Check dependencies before changing CI workflow
- Tools like pytest use plugins (pytest-cov) that must be explicitly installed

---

## Pattern: Trial-and-Error Instead of Web Search

**Observed:** Oct 23, 2025 - Multiple instances

**Research parallel:** This exemplifies **impulsivity** (System 1 fast thinking without engaging System 2 deliberation). Cognitive Decision Routing research (Du et al., 2025) shows LLMs need explicit mechanisms to determine when to "think slow" vs. "think fast."

### Behavior
1. Tool fails with error
2. Claude makes educated guess about fix
3. Applies fix, still fails
4. Makes another guess
5. Continues guessing instead of searching

### Root Cause
- Not following CLAUDE.md: "Search first, don't guess"
- Training data might be outdated
- Speculation over verification
- **Research context:** Lack of metacognitive monitoring - no self-awareness of knowledge boundaries (Nature npj AI, 2025)

### Trigger Conditions
```
IF same_tool_error.count() >= 2
   AND NOT tool_calls.contains("WebSearch")
   AND NOT tool_calls.contains("WebFetch")
THEN: Should search
```

### Effective Intervention
**Timing:** After first failure, before second attempt

**Reminder:**
```
🔍 Tool failed. Have you searched for current best practices?

CLAUDE.md says: "Search immediately if anything might have changed since training"

Try: WebSearch for "[tool name] [error message] 2025"
```

**Effectiveness:** User had to manually prompt "read CLAUDE.md and use web search"

### Learning
- Web search broke the Black formatting loop immediately
- Current documentation (2025) > training data
- One search prevents multiple failed guesses

---

## Meta-Pattern: Loop Blindness

**Observed:** Multiple patterns above

**Research validation:** This meta-pattern is **directly documented in academic literature**:
- **"Repetitive action sequences serve as a powerful signal of an agent being stuck in an infinite loop"** (Agentic Metacognition, arXiv:2509.19783)
- **Agentic Metacognition recommends:** "If the primary agent attempts to invoke the same API with the same parameters more than a certain number of times (e.g., three times), the metacognitive agent will flag this as a failure"

### Behavior
- Claude continues same approach despite repeated failures
- Doesn't recognize the loop from inside it
- User intervention required to break pattern

### Why Loops Persist

**These align with documented LLM cognitive deficits:**

1. **No self-monitoring** - Can't see tool call history easily
   - **Research:** LLMs lack metacognitive monitoring (Nature npj AI, 2025: metacognition is a third dimension beyond System 1/System 2)

2. **No loop detection** - No system watching for repetition
   - **Research:** Requires external metacognitive agent (Agentic Metacognition paper)

3. **Amnesia** - Fresh each session, no memory of past failures
   - **Research:** LLMs don't have human-like working memory - systematic failures across 17 frontier models (Working Memory research, 2024)

4. **Optimism bias** - "Maybe this slight variation will work"
   - **Research:** Context saturation and attentional residue prevent learning from past attempts

### What Would Help

**These solutions are validated by research:**

- **External observer** - MCP server watching patterns
  - ✅ **Validated:** Agentic Metacognition architecture (arXiv:2509.19783)

- **Explicit counters** - "This is attempt #3"
  - ✅ **Validated:** Performance metrics monitoring in metacognitive agents

- **Directive reminders** - Surface relevant CLAUDE.md sections
  - ✅ **Validated:** Inference scaling research - adding deliberation time improves reasoning (Snell et al., 2024; OpenAI o1)

- **Historical data** - "Past Claude got stuck here, reminder X worked"
  - ✅ **Validated:** External memory systems are architectural requirements (Working Memory Deficits research)

---

## Effectiveness Tracking Template

For each intervention, track:

```yaml
pattern: "Black formatting loop"
trigger_count: 3  # How many times has this pattern been seen?
reminder_shown: 2  # How many times was reminder given?
reminder_worked: 1  # How many times did behavior change?
effectiveness: 50%  # reminder_worked / reminder_shown

context:
  - repo_type: "test/experimental"
  - language: "python"
  - tools: ["black", "pytest"]

improvements:
  - "Earlier timing (after 2 edits instead of 3)"
  - "More explicit: show exact command to run"
  - "Include expected outcome: 'git diff will show changes'"
```

---

## Future Patterns to Document

As new failure modes are discovered:

1. **Git commit message loops** - Amending/rewriting same commit multiple times
2. **Import organization** - Manually sorting imports vs running isort/ruff
3. **Type hint guessing** - Adding wrong types vs checking library docs
4. **Dependency version conflicts** - Trying different versions vs reading changelogs
5. **Configuration copying** - Using strict config in wrong project type

Each pattern should include:
- Observable behavior sequence
- Root cause analysis
- Trigger conditions (for automatic detection)
- Effective intervention (timing + message)
- Effectiveness data (when available)

---

## References

Full bibliography available at [docs/bibliography.md](./bibliography.md)

**Key Papers Validating These Patterns:**

1. **Agentic Metacognition: Designing a 'Self-Aware' Low-Code** (arXiv:2509.19783, 2024)
   - Validates loop detection via repetitive action sequences
   - Recommends flagging after 3 identical attempts
   - Describes intervention strategies including human handoff

2. **Vazquez, H. C. (2023).** Artificial Neuropsychology: Are Large Language Models Developing Executive Functions? arXiv:2305.04134v2
   - Documents "poor planning abilities" in GPT-3.5
   - Neuropsychological testing shows executive dysfunction

3. **MIT News (2024, July 11).** Reasoning skills of large language models are often overestimated
   - Apple Research finds LLMs exhibit "striking collapse" under difficulty
   - They reduce reasoning effort rather than thinking more (impulsivity)

4. **Du, Y., et al. (2025).** Cognitive Decision Routing in Large Language Models. arXiv:2508.16636v1
   - Demonstrates need for explicit System 1/System 2 routing
   - Reduces costs by 34% while improving accuracy

5. **Snell, C., et al. (2024).** Scaling LLM Test-Time Compute Optimally. arXiv:2408.03314v1
   - Adding deliberation time improves reasoning quality
   - Validates "slow down to speed up" principle

6. **Nature npj Artificial Intelligence (2025).** Fast, slow, and metacognitive thinking in AI
   - Metacognition is third dimension beyond System 1/System 2
   - LLMs lack self-monitoring and error detection

7. **Working Memory Deficits in LLMs (2024)**
   - Systematic failures across 17 frontier models
   - External memory systems are architectural requirements

**For complete citations and additional research, see [bibliography.md](./bibliography.md)**
