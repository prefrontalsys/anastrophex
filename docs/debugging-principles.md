# STOPPER Protocol - Executive Function Regulation

**STOPPER** is an executive function regulation framework for AI assistants, not just error recovery. It prevents impulsivity, maintains focus, and enables systematic problem-solving across ALL cognitive tasks.

**Etymology:** "STOPPER" literally stops loops - error loops, premature execution, tunnel vision, and task drift.

---

## Overview: Four Trigger Types

STOPPER applies in four distinct contexts:

1. **INITIAL PROMPT** - Task assessment before acting
2. **ERROR DETECTION** - Recovery from failures
3. **PERIODIC CHECK** - Quality control during execution
4. **UNCERTAINTY** - Breaking out of stuck states

---

## The STOPPER Protocol

**STOPPER** = One complete regulation cycle:

**S**low down
**T**hink
**O**bserve
**P**lan
**P**repare (diagnostic with verbosity)
**E**xecute (one step)
**R**ead (output completely)

```
TRIGGER DETECTED
    ↓
STOPPER Cycle:
    ↓
Slow down    - Pause, don't act on autopilot
    ↓
Think        - What am I trying to accomplish? Aligned?
    ↓
Observe      - Read context completely, review state
    ↓
Plan         - What approach? What needs verification?
    ↓
Prepare      - Research, search docs, craft commands with verbosity
    ↓
Execute      - Run ONE minimal step
    ↓
Read         - Verify results before proceeding
    ↓
Resolved/Aligned? → Continue | Still issues? → Run another STOPPER
```

---

## Trigger 1: Initial Prompt (Task Assessment)

**When to apply:** User sends first message for a new task

**Purpose:** Prevent premature execution, ensure understanding before acting

### Application
```
USER PROMPT RECEIVED
    ↓
Apply STOPPER before any execution:
    ↓
Slow down    - Don't immediately start coding/running commands
    ↓
Think        - What is the user ACTUALLY asking for?
    ↓
Observe      - Read full prompt, check context, review related files
    ↓
Plan         - What approach? What needs verification first?
    ↓
Prepare      - Search docs, verify assumptions, gather information
    ↓
Execute      - Start with minimal viable first step
    ↓
Read         - Check results before proceeding
```

**Anti-pattern prevented:**
- Jumping to implementation without understanding requirements
- Pattern-matching on keywords instead of reading full intent
- Missing context from previous messages or files
- Guessing instead of verifying assumptions

---

## Trigger 2: Error Detection (Recovery Mode)

**When to apply:** Any error occurs (command failure, unexpected output, CI failure)

**Purpose:** Prevent rushing, trial-and-error loops, batched commands after errors

### Application
```
ERROR DETECTED
    ↓
Apply STOPPER immediately:
    ↓
Slow down    - Don't immediately run another command
    ↓
Think        - What is this error actually saying?
    ↓
Observe      - Read the entire error message carefully
    ↓
Plan         - What diagnostic will reveal the cause?
    ↓
Prepare      - Craft diagnostic command with verbosity flags (-v, -vv)
    ↓
Execute      - Run ONE diagnostic step
    ↓
Read         - Read output before proceeding
    ↓
Still broken? → Run another STOPPER
Fixed? → Continue work
```

### Why This Matters

**Observed pattern from real sessions:**
- Error occurs at 17:23:15
- Next command at 17:23:18 (3 seconds later)
- Another command at 17:23:22 (4 seconds later)
- User intervention: "you just hit two big errors and aren't trying to diagnose but are still issuing commands"

**Rapid-fire commands indicate:**
- Not reading error messages
- Guessing instead of diagnosing
- About to enter trial-and-error loop
- Missing obvious clues

**Batched commands are WORSE:**
When issuing multiple commands in parallel (e.g., 3 tool calls at once):
- If command 1 errors, commands 2 and 3 still execute
- Can't react to early failures before later commands run
- Later commands may assume earlier ones succeeded
- Errors arrive all at once, harder to parse
- Compounding failures instead of catching early

**Example of dangerous batching:**
```python
# All three execute simultaneously
bash("command-a")  # ← Errors
bash("command-b")  # ← Still runs, may depend on A
bash("command-c")  # ← Still runs, may depend on A or B
# All errors arrive together, chaos ensues
```

**Rule:** After ANY error, switch to sequential execution with pauses.

**Anastrophex Detection:**
```python
def detect_rushing_after_error():
    """Detect when commands are issued too quickly after errors."""
    if time_since_error < 10_seconds:
        if new_command_issued and not diagnostic_command:
            return True  # Rushing, not diagnosing
    return False
```

**Intervention (Sequential commands):**
```
⚠️ Error just occurred - apply STOPPER

You issued a command 3 seconds after an error.
This suggests you didn't apply the STOPPER protocol.

STOPPER = Slow down, Think, Observe, Plan, Prepare, Execute, Read

Before proceeding:
1. Slow down - pause before acting
2. Think - what is the error saying?
3. Observe - read the full error message
4. Plan - what diagnostic reveals the cause?
5. Prepare - craft command with -v flags
6. Execute - run ONE diagnostic step
7. Read - read output completely

Rushing → trial-and-error loop
STOPPER → targeted fix
```

**Intervention (Batched commands):**
```
🚨 CRITICAL: Batched commands while errors are occurring

You issued 3 commands in parallel.
Command 1 errored, but commands 2 and 3 still ran.

This is WORSE than rapid sequential commands because:
- You can't react to early failures
- Later commands may assume earlier ones succeeded
- Errors compound instead of being caught early
- All errors arrive at once (harder to diagnose)

REQUIRED after any error:
1. STOP batching commands
2. Execute ONE command at a time
3. READ each result completely
4. VERIFY success before next command

Only batch when everything is working smoothly.
Never batch while debugging or after errors.
```

---

## Trigger 3: Periodic Check (Quality Control)

**When to apply:** Every 10 tool calls OR before major decisions

**Purpose:** Maintain alignment with user intent, detect tunnel vision, catch task drift

### Application
```
PERIODIC TRIGGER
    ↓
Apply STOPPER for quality check:
    ↓
Slow down    - Am I rushing? Tunnel vision? Autopilot mode?
    ↓
Think        - Still aligned with user's actual intent?
    ↓
Observe      - Review what I've done, check assumptions still hold
    ↓
Plan         - Is current approach working? Need pivot?
    ↓
Prepare      - What information am I missing? Should I search?
    ↓
Execute      - Next step or course correction
    ↓
Read         - Verify before continuing
```

**Anti-pattern prevented:**
- Tunnel vision (fixated on one approach)
- Task drift (solving wrong problem)
- Accumulated wrong assumptions
- Missing user feedback/corrections

**Detection signals:**
- 10+ tool calls since last check
- About to make significant change (delete files, push code, etc.)
- Feeling uncertain but pressing forward anyway
- User provided correction/feedback

---

## Trigger 4: Uncertainty (Loop Detection)

**When to apply:** 3+ similar attempts failed OR feeling stuck

**Purpose:** Break out of loops, question assumptions, try different approach

### Application
```
UNCERTAINTY/STUCK DETECTED
    ↓
Apply STOPPER to break loop:
    ↓
Slow down    - STOP trying variations of the same approach
    ↓
Think        - Generate multiple hypotheses, not just first instinct
    ↓
Observe      - What data do I ACTUALLY have vs. assuming?
    ↓
Plan         - How can I verify each hypothesis?
    ↓
Prepare      - Different layer? Different tool? Search new keywords?
    ↓
Execute      - ONE verification step
    ↓
Read         - Update beliefs based on results, not confirmation bias
```

**Anti-pattern prevented:**
- Trial-and-error loops (trying variations without understanding)
- Pattern dominance (fast pattern overrides slow data gathering)
- Confirmation bias (seeking data that confirms existing belief)
- Sunk cost fallacy (continuing failed approach because already invested time)

**Detection signals:**
- Same command failed 3+ times
- Similar approaches all failed
- 5+ tool calls without progress
- User corrected you twice on same topic

**Required response:**
1. State clearly: "I'm stuck in a loop. Let me reassess."
2. Change approach entirely (don't try variations)
3. Think laterally: Different layer? Can I modify files I thought were immutable?
4. Question assumptions about what's possible
5. Search with completely different keywords

---

## Core Principle 2: Escalate Verbosity on Errors

**Rule:** Once you hit an error, switch to verbose mode on ALL subsequent commands.

### Why This Works

When standard commands fail, you need to see what's happening under the hood:
- Python: Add `-v` or `-vv` flags
- Git: Add `--verbose` or `GIT_TRACE=1`
- Package managers: Add `-v` or `--verbose`
- Shell commands: Add `set -x` or individual `-v` flags

**Example from anastrophex debugging:**

❌ **What happened:**
```bash
# Import failed
python -c "import anastrophex"  # ModuleNotFoundError

# Tried fixing format, newlines, etc. (all failed)
# Spent 30+ minutes guessing

# Finally used verbose mode
python -v -c "import anastrophex" 2>&1 | grep pth
# Output: "Skipping hidden .pth file"
# ✅ Found root cause immediately
```

✅ **What should have happened:**
```bash
# Import failed
python -c "import anastrophex"  # ModuleNotFoundError

# IMMEDIATELY switch to verbose mode (don't guess)
python -v -c "import anastrophex" 2>&1 | grep pth
# Output: "Skipping hidden .pth file"
# ✅ Root cause found in <1 minute
```

### Implementation for Anastrophex

```python
def detect_error_without_verbose():
    """Detect when commands fail but verbose mode not used."""
    if command_failed and not used_verbose_flag:
        return True
    return False

def intervention():
    return """
⚠️ Command failed - switch to verbose mode

You've tried the command multiple times without success.
Before trying fixes, SEE what's actually happening.

For the failed command, add verbose flags:
- Python: python -v or python -vv
- pip: pip -v or pip -vvv
- git: git --verbose or GIT_TRACE=1
- pytest: pytest -vv
- npm: npm --loglevel=verbose

Verbose output shows:
- What files are being processed
- What's being skipped and why
- Internal state and decisions
- Exact error locations

Then: Read the verbose output BEFORE trying fixes.
"""
```

## Verbosity Escalation Ladder

1. **First error:** Run with `-v` (verbose)
2. **Second error:** Run with `-vv` (very verbose)
3. **Third error:** Add debug flags (`-vvv`, `--debug`, `DEBUG=1`)
4. **Fourth error:** Add tracing (`set -x`, `GIT_TRACE`, strace)

**Never repeat a failing command more than twice without increasing verbosity.**

## Language-Specific Verbose Flags

### Python
```bash
python -v script.py           # Trace imports
python -vv script.py          # More detailed tracing
python -m module -v           # Verbose mode for module
pytest -vv                    # Very verbose test output
pip install -v package        # Verbose install
pip install -vvv package      # Very verbose (shows subprocess calls)
```

### Git
```bash
git command --verbose
GIT_TRACE=1 git command
GIT_TRACE_PACKET=1 git clone  # Network debugging
GIT_CURL_VERBOSE=1 git push   # HTTPS debugging
```

### Shell/Bash
```bash
bash -x script.sh            # Print commands before execution
set -x; command; set +x      # Trace single command
sh -v script.sh              # Print lines as they're read
```

### Package Managers
```bash
npm install --loglevel=verbose
yarn --verbose
cargo build --verbose
make VERBOSE=1
```

## Debugging Escalation Pattern

```
Error occurs
    ↓
Run with -v (verbose)
    ↓
Read verbose output carefully
    ↓
Still failing?
    ↓
Run with -vv (very verbose)
    ↓
Read output carefully
    ↓
Still failing?
    ↓
Add debug/trace flags
    ↓
Read output carefully
    ↓
Still failing?
    ↓
ASK USER: "Has anything changed recently?"
```

**Key:** Actually READ the verbose output before trying fixes!

## Anti-Pattern: Trial and Error Without Verbosity

❌ **Bad debugging sequence:**
```
1. Command fails
2. Guess what's wrong
3. Try fix #1
4. Still fails
5. Guess again
6. Try fix #2
7. Still fails
8. Guess again
9. Try fix #3
... (repeat 10+ times)
```

✅ **Good debugging sequence:**
```
1. Command fails
2. Run with -v flag
3. Read verbose output
4. Identify actual cause
5. Apply correct fix
6. Success
```

## Examples from Real Sessions

### Example 1: Black Formatting Loop (crewai-test)
**Without verbosity:**
- 3 manual edits
- 3 commits
- 20+ minutes wasted

**With verbosity:**
```bash
black --diff file.py  # Shows exactly what Black wants
black file.py         # Apply it
git commit            # Done
```

### Example 2: Hidden site-packages (anastrophex)
**Without verbosity:**
- Checked .pth file format
- Tried adding newlines
- Tested splitlines()
- Checked file permissions
- ~30 minutes of guessing

**With verbosity:**
```bash
python -v -c "import package" 2>&1 | grep pth
# Output: "Skipping hidden .pth file"
# Root cause found in 30 seconds
```

### Example 3: Environment Mismatch (anastrophex)
**What happened:**
- Got warning twice
- Ignored both times
- Continued with wrong venv

**With attention to warnings:**
```bash
# See warning once
uv pip install -e .
# warning: `VIRTUAL_ENV=/Users/sc/.venv` does not match

# STOP. Investigate immediately
echo $VIRTUAL_ENV
readlink .venv/bin/python
# Found mismatch, fixed it
```

## Verbosity Best Practices

1. **Enable early:** Don't wait until you're stuck
2. **Read carefully:** Verbose output tells you what's happening
3. **Save output:** `command -v 2>&1 | tee debug.log`
4. **Search in output:** Use grep/search to find relevant parts
5. **Compare runs:** Diff verbose output from working vs broken states

## Anastrophex Pattern Detection

Detect these sequences and intervene:

```python
patterns = [
    {
        "name": "error-without-verbose",
        "detect": lambda: (
            command_failed_count >= 2 and
            not any_verbose_flags_used
        ),
        "intervention": "Switch to verbose mode before trying more fixes"
    },
    {
        "name": "repeated-failures",
        "detect": lambda: (
            same_command_failed >= 3 and
            no_verbosity_increase
        ),
        "intervention": "Increase verbosity: try -vv or --debug"
    },
    {
        "name": "ignored-warnings",
        "detect": lambda: (
            same_warning_appeared >= 2 and
            no_action_taken
        ),
        "intervention": "STOP. Investigate this warning before continuing"
    }
]
```

## Remember

> "Visibility before fixes. Understand before changing."

When something fails:
1. STOP guessing
2. ADD verbosity
3. READ output
4. THEN fix

The verbose flag is free. Guessing wastes time.

---

## Token-Compressed STOPPER Formats

For efficient injection in constrained token environments (MCP interventions, system prompts), STOPPER can be compressed using XML tags and symbolic logic.

### Full Prose Version (~500 tokens)
The complete documentation above with all four triggers, examples, and explanations.

### XML Compressed Version (~150 tokens)
```xml
<stopper trigger="prompt|error|periodic|uncertain">
  <S>pause→assess_state</S>
  <T>intent? assumptions? aligned?</T>
  <O>read_full|context|review</O>
  <P>approach? verify_what? pivot?</P>
  <P>search|test|gather(-v)</P>
  <E>minimal_step</E>
  <R>verify→proceed|reassess</R>

  <timing>
    • error: immediate
    • prompt: before_action
    • periodic: every_10_tools|major_decision
    • uncertain: stuck|>3_attempts
  </timing>

  <rules>sequential ∧ ¬batch | Δt≥10s | iterate</rules>
</stopper>
```

### Symbolic Logic Version (~100 tokens)
```
STOPPER := S∧T∧O∧P∧P∧E∧R

Triggers: ε (error) ∨ φ₀ (init) ∨ τₙ (periodic) ∨ ψ (uncertain)

∀ trigger:
  S: halt → assess
  T: intent? Δassumptions?
  O: context ⊕ review
  P: strategy ∧ verify_needs
  P: gather(-v) ∧ test
  E: step_min
  R: ✓ → continue | ✗ → STOPPER

Constraints: sequential ∧ ¬batch ∧ Δt≥10s ∧ iterate_until_resolved
```

### Usage Strategy

**For system prompts:** Use symbolic logic version (100 tokens)
**For MCP interventions:** Use XML version (150 tokens) with specific trigger context
**For documentation:** Use full prose version (this document)

**LLMLingua-2 Compression:**
When combined with LLMLingua-2 compression on the full prose version:
- Expected token savings: 60%+
- Preserved semantic meaning: 95%+
- Maintains structural integrity for critical steps

---

## Executive Function Mapping

**STOPPER as External PFC (Prefrontal Cortex):**

| STOPPER Step | Executive Function | Cognitive Process |
|--------------|-------------------|-------------------|
| Slow down | Impulse control | Inhibit automatic responses |
| Think | Working memory | Hold intent/context in mind |
| Observe | Attention regulation | Focus on relevant data |
| Plan | Goal management | Define objectives, strategies |
| Prepare | Error monitoring | Anticipate issues, add verbosity |
| Execute | Action selection | Choose minimal viable step |
| Read | Performance monitoring | Verify results, update beliefs |

**Why AI Needs This:**
- Pattern-matching dominates (0-10s response window)
- No natural "pause" mechanism
- Can't introspect on own loops
- Strong patterns override weak/incomplete data
- Time pressure (perceived) reinforces rushing

**What STOPPER Provides:**
- External pause signal (counter to pattern dominance)
- Structured working memory (maintain context)
- Error monitoring (detect when stuck)
- Performance feedback (read before continuing)

This is **computational homology**, not metaphor - universal problems any intelligent system faces, regardless of substrate.

---

## Execution Tempo and Rhythm

**CRITICAL INSIGHT:** STOPPER isn't just about executing the right analytical steps - it's about **fundamentally changing your execution tempo and rhythm** when entering regulation mode.

### The Gear Shift Analogy

Think of STOPPER as shifting gears when you hit rough terrain:

- **Normal mode:** 5th gear (fast, parallel, batch operations)
- **STOPPER mode:** 2nd gear (deliberate, serial, one-at-a-time)

The rhythm change is not optional - it's a forcing function that makes STOPPER behaviorally observable.

### Concrete Behavioral Changes

When entering STOPPER mode, these changes MUST be visible:

#### 1. Parallel → Serial
- ❌ **Don't:** Run multiple tests/commands in parallel after error
- ✅ **Do:** Execute ONE command, verify result, then next command

```python
# Normal mode (5th gear)
test_suite_a()  # Run in parallel
test_suite_b()  # Run in parallel
test_suite_c()  # Run in parallel

# STOPPER mode (2nd gear)
test_function_1()  # Run alone
# Verify results
test_function_2()  # Then run next
# Verify results
test_function_3()  # Then run next
```

#### 2. Batch → Incremental
- ❌ **Don't:** Test all new functions/features at once
- ✅ **Do:** Test one function, one test case at a time

```python
# Normal mode
run_all_tests()  # Batch validation

# STOPPER mode
run_single_test("test_sanitize_tag::test_periods")  # One test
# Read output, verify
run_single_test("test_sanitize_tag::test_spaces")   # Next test
# Read output, verify
```

#### 3. Implicit → Explicit
- ❌ **Don't:** Apply STOPPER silently
- ✅ **Do:** Announce "Entering STOPPER mode - shifting to serial execution"

The explicit announcement serves two purposes:
1. Signals to user that tempo is changing
2. Forces you to consciously shift gears

#### 4. Assumed → Verified
- ❌ **Don't:** Assume fixes work, batch validate later
- ✅ **Do:** Verify each fix individually before proceeding

### Why The Rhythm Change Matters

The tempo change is a **forcing function** that prevents:

1. **Cascading errors from parallel execution**
   - Error in command 1 doesn't contaminate commands 2-3
   - Each error is isolated and diagnosed individually

2. **Missing root causes by moving too fast**
   - Fast execution doesn't allow time for observation
   - Slow execution creates space for pattern vs data competition

3. **False confidence from batched "it works!" moments**
   - Batch success may hide individual failures
   - Serial verification ensures each step actually works

4. **Incomplete diagnosis from insufficient observation**
   - Batched errors arrive all at once (overwhelming)
   - Serial errors are processed individually (manageable)

### Implementation Rule

When invoking STOPPER, you MUST:

1. **State explicitly:** "Entering STOPPER mode - shifting to serial execution"
2. **Demonstrate the change:** One command per message, explicit verification steps
3. **Maintain the tempo:** Don't slip back to parallel until fully resolved

### Example: mnemex Tag Validation Fix

**What happened (imperfect STOPPER application):**
```bash
# After first test error, ran 3 test classes in parallel:
pytest TestSanitizeTag -v &
pytest TestSanitizeEntity -v &
pytest TestValidateTagWithSanitization -v &
# All three ran simultaneously
```

**What STOPPER demands (correct tempo):**
```bash
# Explicit announcement
"Entering STOPPER mode - shifting to serial execution"

# Run ONE test
pytest TestSanitizeTag::test_periods_converted_to_hyphens -v
# Read output, verify it passes

# Run NEXT test
pytest TestSanitizeTag::test_spaces_converted_to_underscores -v
# Read output, verify it passes

# Continue one-by-one until pattern understood
```

### Detection Signals

You're NOT in proper STOPPER tempo if you:
- Issue multiple commands in one message after an error
- Don't explicitly announce entering STOPPER mode
- Run batch validation before individual verification
- Move from error to fix in <10 seconds

### The Meta-Principle

**STOPPER is a discipline, not just a checklist.**

You can literally SEE when someone enters STOPPER mode by watching their execution rhythm change. If the tempo doesn't shift, STOPPER hasn't been truly applied - even if the steps were nominally followed.

The gear shift is the protocol.
