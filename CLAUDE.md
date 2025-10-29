# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Anastrophex** is an MCP (Model Context Protocol) server that helps AI assistants recognize and break out of failure patterns by:
1. Detecting loops in tool calls and command patterns
2. Injecting timely directives from CLAUDE.md/AI_CREDO.md
3. Learning what works via mnemex integration

**Current Status:** Planning/Design Phase - foundational MCP server structure exists, pattern detection not yet implemented.

## Development Commands

### Testing
```bash
# Run all tests
uv run pytest

# Run tests with verbose output
uv run pytest -vv

# Run specific test
uv run pytest tests/test_server.py::test_list_resources
```

### Code Quality
```bash
# Format code (auto-fix)
uv run black src/ tests/

# Check formatting only
uv run black --check src/ tests/

# Lint and auto-fix
uv run ruff check --fix src/ tests/

# Type checking
uv run mypy src/
```

### Running the Server
```bash
# Run as installed command
anastrophex

# Or run as module
python -m anastrophex.server

# Development installation
uv pip install -e ".[dev]"
```

## Architecture

### Three-Layer System

```
┌──────────────────────────────────────────────┐
│ 1. Pattern Detection                         │
│    - Tool call history monitoring            │
│    - Similar commands with variations        │
│    - Failed attempt counter                  │
└──────────────────────────────────────────────┘
              ↓
┌──────────────────────────────────────────────┐
│ 2. Directive Injection                       │
│    - Pull from ~/.claude/CLAUDE.md           │
│    - Context-aware reminders                 │
│    - Lightweight alerts (not blocking)       │
└──────────────────────────────────────────────┘
              ↓
┌──────────────────────────────────────────────┐
│ 3. Learning via Mnemex                       │
│    - Write findings to mnemex graph          │
│    - Track what reminders work               │
│    - Persist across Claude sessions          │
└──────────────────────────────────────────────┘
```

### Key Components

**Pattern Detector** (src/anastrophex/server.py:15-21)
- Monitors tool_history for repetitive patterns
- Currently stub implementation - stores history but doesn't analyze

**MCP Resources** (exposed via anastrophex://)
- `patterns/all` - All known behavior patterns
- `alerts/active` - Currently triggered patterns
- `directives/all` - Directives from CLAUDE.md

**MCP Tools**
- `report_outcome` - Report intervention effectiveness
- `query_pattern_history` - Check if pattern seen before

### Directory Structure

```
src/anastrophex/
  __init__.py       - Package initialization
  server.py         - Main MCP server implementation
tests/
  test_server.py    - Async tests for MCP handlers
docs/
  architecture.md           - Detailed system design
  failure-patterns.md       - Real debugging failure patterns
  debugging-principles.md   - Core debugging principles (STOPPER protocol)
  cognitive-model.md        - Neuroanatomical model theory
  prompt-compression-strategy.md - Token optimization approach
examples/
  crewai-test-case-study.md      - Oct 2025 CI/CD debugging case
  anastrophex-venv-debugging.md  - venv environment debugging case
```

## Implementation Roadmap

**Phase 1 (Weeks 1-2):** Pattern Detection
- Basic loop detection without intervention
- Black formatting loop (3+ Edit calls to .py files)
- Log patterns to mnemex

**Phase 2 (Weeks 3-4):** Directive Injection
- Parse CLAUDE.md for directives
- Inject timely reminders via tool result augmentation
- Trigger after N-1 attempts, before Nth

**Phase 3 (Weeks 5-6):** Learning System
- Track effectiveness (shown_count, worked_count)
- Query mnemex for pattern history
- Select best intervention based on past success

**Phase 4 (Weeks 7-8):** Advanced Patterns
- Multi-tool sequences (Edit → Push → CI fail → Edit)
- Time-based detection (rapid attempts < 1 minute)
- Context filters (repo_type, language, tool_name)

See ROADMAP.md and TODO.md for detailed tasks.

## Critical Design Principles

### The STOPPER Protocol (from docs/debugging-principles.md:1-662)

**STOPPER** is an executive function regulation framework for AI assistants, not just error recovery.

**STOPPER** = **S**low down, **T**hink, **O**bserve, **P**lan, **P**repare, **E**xecute, **R**ead

**Etymology:** "STOPPER" literally stops loops - error loops, premature execution, tunnel vision, task drift.

#### Four Trigger Types

1. **INITIAL PROMPT** - Apply before executing on user's first message (prevents premature action)
2. **ERROR DETECTION** - Apply immediately when errors occur (prevents rushing/trial-and-error)
3. **PERIODIC CHECK** - Apply every 10 tool calls or before major decisions (maintains alignment)
4. **UNCERTAINTY** - Apply when stuck or 3+ similar attempts failed (breaks loops)

#### Protocol Flow
```
TRIGGER DETECTED
    ↓
STOPPER Cycle:
    ↓
Slow down    - Pause, don't act on autopilot
    ↓
Think        - What am I trying to accomplish? Still aligned?
    ↓
Observe      - Read context completely, review state
    ↓
Plan         - What approach? What needs verification?
    ↓
Prepare      - Research, search docs, craft commands with -v flags
    ↓
Execute      - Run ONE minimal step
    ↓
Read         - Verify results before proceeding
    ↓
Resolved/Aligned? → Continue | Still issues? → Run another STOPPER
```

#### Compressed Format (for token efficiency)
```xml
<stopper trigger="prompt|error|periodic|uncertain">
  <S>pause→assess_state</S>
  <T>intent? assumptions? aligned?</T>
  <O>read_full|context|review</O>
  <P>approach? verify_what? pivot?</P>
  <P>search|test|gather(-v)</P>
  <E>minimal_step</E>
  <R>verify→proceed|reassess</R>
</stopper>
```

**Critical Rules:**
- After ANY trigger, use sequential execution (no batching)
- Minimum 10s between error and next command, recommended 30s
- Each STOPPER = one complete cycle
- Batched commands after errors are WORSE than rushing (errors compound, can't react mid-batch)

### Real Failure Patterns

**Black Formatting Loop** (docs/failure-patterns.md:5-53)
- Trigger: 3+ Edit calls to .py files + CI Black failure + no `black` tool call
- Root cause: Guessing what formatter wants instead of running it
- Solution: `black file.py` then commit the exact changes

**Mypy Strict Config Mismatch** (docs/failure-patterns.md:56-107)
- Trigger: 50+ mypy errors from external library + `strict = true` in config
- Root cause: Production config copy-pasted into test/experimental repo
- Solution: `ignore_missing_imports = true` or relax strictness

**Missing Dependency Misdiagnosis** (docs/failure-patterns.md:109-151)
- Trigger: "unrecognized arguments: --cov" + no pytest-cov in deps
- Root cause: Error message is literal - missing plugin
- Solution: Add pytest-cov to dev dependencies

**Trial-and-Error Without Web Search** (docs/failure-patterns.md:153-196)
- Trigger: Same error 2+ times + no WebSearch/WebFetch calls
- Root cause: Guessing instead of verifying current best practices
- Solution: Use WebSearch immediately after first failure

## Testing Guidelines

- All MCP handlers must have async tests
- Use pytest fixtures for server instances (tests/test_server.py:8-11)
- Test resources, tools, and error handling separately
- Use `pytest.mark.asyncio` for async test functions

## Type Checking Configuration

**This is a production package** (will publish to PyPI), so:
- `strict = true` in mypy config
- All functions must have type annotations
- Use `disallow_untyped_defs = true`

If external dependencies lack type stubs, add module-specific overrides:
```toml
[[tool.mypy.overrides]]
module = "mcp.*"
ignore_missing_imports = true
```

## Core Philosophy

**Incremental gains over perfection:**
- 30% success rate is a win (better than 0%)
- Catch *some* loops, improve over time
- "Pobody's nerfect" - build what helps, iterate based on real usage

**Persistence across sessions:**
- Claude doesn't persist, but MCP server and mnemex do
- System learns which patterns/reminders work
- Each new Claude session benefits from past learnings

**Effectiveness tracking is core value:**
- Every intervention recorded with context
- Track what works (worked_count / shown_count)
- Adapt strategies based on real data

## Key Documentation

- **docs/architecture.md** - Complete system design with mnemex graph structure
- **docs/failure-patterns.md** - Real patterns from debugging sessions with effectiveness tracking
- **docs/debugging-principles.md** - The STOPPER protocol and verbosity escalation
- **TODO.md** - SMART tasks (Specific, Measurable, Achievable, Relevant, Time-bound)
- **examples/** - Real case studies from Oct 2025 debugging sessions

## Performance Requirements

- Tool call monitoring: < 10ms overhead
- Mnemex queries: cached, indexed
- Pattern matching: efficient (indexed by signature)
