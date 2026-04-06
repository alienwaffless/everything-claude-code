# ECC Installation Guide (WSL Edition)

This guide covers how to install the **Everything Claude Code (ECC)** repository for three integration targets: **Claude Code**, **Codex**, and **Antigravity**. It is specifically tailored for developers running on **Windows Subsystem for Linux (WSL)**.

## 1. WSL Prerequisites: The "Line Endings" Rule

Before installing ECC on WSL, you must ensure that your Git environment and files use Unix-style (`LF`) line endings. The installation scripts and internal hooks are evaluated in a Linux bash environment by WSL; Windows-style (`CRLF`) line endings will cause "bad interpreter" and parsing errors.

1. **Clone inside WSL:** Clone the repository into your WSL file system (e.g., `~/Projects/`), not on a mounted Windows drive (e.g., `/mnt/c/`).
2. **Git Configuration:** Ensure Git doesn't auto-convert your files to CRLF.
   ```bash
   git config --global core.autocrlf input
   ```
3. **Normalize Existing Files:** If you suspect line ending pollution, use `dos2unix` before installing:
   ```bash
   sudo apt-get install dos2unix
   find . -type f -name "*.sh" -exec dos2unix {} +
   find . -type f -name "*.js" -exec dos2unix {} +
   ```

## 2. Universal Installation Script

ECC uses a central, Node-based selective installer accessed via `./install.sh`. Run the installer from the root of your cloned ECC directory.

```bash
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code
npm install
```

### Target 1: Claude Code (Global Install)
Claude Code maps configurations globally. The installer defaults to the Claude Code target (`claudeHome`), making it the easiest to initialize.

**Command:**
```bash
# Target is implied as "claude" by default
./install.sh --profile full
```

**What it does:**
- Maps 36 Specialized Agents to `~/.claude/agents/`
- Maps 147 Workflow Skills to `~/.claude/skills/`
- Maps Flat Rules to `~/.claude/rules/`
- Emits state registry to `~/.claude/ecc-install-state.json`

### Target 2: Codex App (Global Install)
Codex relies on the exact same file structures as Claude, but expects them to be located in its own global directory (`~/.codex/`).

**Command:**
```bash
./install.sh --target codex-home --profile full
```
*(You may also use `--target codex` as an alias depending on your ECC version).*

**What it does:**
- Maps all agents, skills, and rules to `~/.codex/` rather than `~/.claude/`.
- Emits state registry to `~/.codex/ecc-install-state.json`.

### Target 3: Antigravity (Project-Local Install)
Antigravity uses a different architectural paradigm. Instead of a global installation, it expects a **project-scoped** `.agent/` directory inside your specific repository.

> [!NOTE] 
> Because Antigravity is project-scoped, you generally install ECC *into* the project you are working on. Run this command inside your specific project directory if using `npx ecc-install`, or point the execution path locally.

**Command:**
```bash
# Inside the working directory you intend to use Antigravity with
./install.sh --target antigravity --profile full
```

**What it does:**
Because Antigravity handles terminology uniquely, the installer automatically remaps the directories:
- "Commands" become workflows: Installed to `.agent/workflows/`
- "Agents" become skills: Installed to `.agent/skills/`
- Rules are flattened: Installed to `.agent/rules/`
- Emits state registry to `.agent/ecc-install-state.json`

## 3. Verifying the Installation

After running the installer for your target setup, always perform a health check to ensure everything was correctly mapped.

```bash
# For Claude Code
node scripts/doctor.js --target claude-home

# For Codex
node scripts/doctor.js --target codex-home

# For Antigravity
node scripts/doctor.js --target antigravity
```

### Dealing with "Drifted" Files
If the `doctor.js` script reports `[warning] drifted-managed-files`, it means your target directory is out of sync with the main repository. This is common in WSL environments if line-endings get unexpectedly modified by Git.

To instantly fix drift, use the repair tool:
```bash
node scripts/repair.js --target <claude-home | codex-home | antigravity>
```
