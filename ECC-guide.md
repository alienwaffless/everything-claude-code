# The Unofficial ECC User Guide: Workflow & Multi-Agent Operations

Welcome to the **Everything Claude Code (ECC)** operational guide. This document is designed for end-users working across **Claude Code**, **Codex**, and **Antigravity**. It focuses strictly on practical, day-to-day workflow mechanics: what tools are available, how they work, and how they apply across different IDE environments.

## 1. What is ECC?

ECC is not just a collection of system prompts. It is an **Agent Harness Performance System** that introduces:
- **Agents (`agents/`)**: Specialized sub-agents like `planner`, `python-reviewer`, `database-reviewer`.
- **Skills (`skills/`)**: Canonical workflows and domain knowledge files (like `python-patterns`, `api-design`).
- **Commands (`commands/`)**: Legacy slash-entries, mapping specific triggers like `/plan` and `/tdd`.
- **Rules (`rules/`)**: Strict guardrails injected persistently in each run.

## 2. Platform Compatibility & Nuance

The primary difference for end-users isn't *how* the tools work, but *where* they live. ECC supports multiple harnesses via selective installation.

| Feature | Claude Code | Codex | Antigravity |
|---------|-------------|-------|-------------|
| **Install Target**| `claude-home` (`~/.claude/`) | `codex-home` (`~/.codex/`) | `antigravity` (`.agent/`) |
| **Scope**| User-level (Global) | User-level (Global) | Project-level (Local) |
| **Command Mapping**| `commands/` | `commands/` | `workflows/` |
| **Agent Mapping**| `agents/` | `agents/` | `skills/` |

### Key Takeaway for Your Workflow:
- If you use **Antigravity**, you must re-initialize the installation in each new local repo (`.agent/`). It treats "Commands" as "Workflows" and "Agents" as "Skills". 
- If you use **Claude Code** or **Codex**, the install is global (`~/`), and slash-commands automatically apply to all projects. 

## 3. The "Golden Cycle": Core Workflow Execution

Whether you are using Antigravity's UI loops or Claude Code's CLI, the canonical way to develop using ECC is the **Golden Cycle**. Stop manually prompting and start relying on the pipeline.

### Step A: The Blueprint Phase (`/plan`)
Never jump straight into writing code for complex tasks.
1. **Trigger**: Invoke `/plan` or the `planner` agent.
2. **Context**: Provide your requirements (e.g., "Add an authentication endpoint using FastAPI").
3. **Outcome**: The agent will create a step-by-step implementation blueprint. Wait for it to prompt you for final approval.

### Step B: Test-Driven Execution (`/tdd`)
Implementation happens inside a quality gate.
1. **Trigger**: Invoke `/tdd` or delegate to `tdd-guide`. 
2. **Context**: Tell it to execute Step 1 of the new business plan.
3. **Outcome**: It will write tests first (RED), write the minimal logic (GREEN), and finally refactor (IMPROVE), ensuring minimum 80% coverage.

### Step C: Expert Review (`/code-review` & Verification)
Never push raw generation without applying specialized guardrails.
1. **Trigger**: Invoke `/code-review`, `/python-review`, or `/rust-review` based on your stack.
2. **Outcome**: The specialized reviewer agent (e.g., `typescript-reviewer`) activates, scans the new code, and enforces ECC rules.

## 4. Specialized Stack Workflows

ECC installs domain-specific skills for granular operations.

### Data Science, Python & Backend (`python-patterns`, `api-design`)
By default, the Python reviewer expects type-hinting, strict memory footprint (like `__slots__`), and EAFP (Easier to Ask Forgiveness than Permission) style error handling. Furthermore, the `api-design` skill enforces strict REST paradigms (e.g., always returning `201 Created` via `POST` with `Location` headers).

### Frontend React (`frontend-patterns`)
When working in TypeScript/NextJS, invoke frontend patterns to force the AI away from messy inheritance and towards Compound Components, React Provider patterns, and strict hook memoization (`useCallback`/`useMemo`).

### Security Logging (`/security-scan`)
A vital tool for production apps. Instead of manually checking APIs for injection or CSRF logic, use `/security-scan` so the `security-reviewer` checks tokens, headers, and secrets handling asynchronously.

## 5. Maintenance and Troubleshooting

Because ECC spans different ecosystems, environmental drift can occur (especially in setups like WSL where line-endings matter).

**Diagnostic Commands**:
If an agent starts ignoring commands or breaking the workflow, verify your installation state using the universal doctor script (run from within the cloned `everything-claude-code` local repo):

- **Antigravity**: `node scripts/doctor.js --target antigravity`
- **Codex**: `node scripts/doctor.js --target codex-home`
- **Claude Code**: `node scripts/doctor.js --target claude-home`

If warnings appear (e.g., `drifted-managed-files`), follow up with `node scripts/repair.js --target <environment>` to restore full ECC functionality.
