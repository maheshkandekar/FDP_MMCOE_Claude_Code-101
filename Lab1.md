## 🧪 Hands-On Lab: Install Claude Code in VS Code & Explore Plan Mode

> **Estimated Time:** 45–60 minutes  
> **Prerequisites:** Laptop with VS Code installed, paid Claude account (Pro/Max/Teams/Enterprise or API key)

---

### Lab Overview

In this lab you will:
- Install Claude Code in VS Code
- Authenticate with your Anthropic account
- Run your first prompt
- Explore Plan Mode on a sample project
- Reflect on the agentic loop in action

---

### Step 1 — Verify Prerequisites

Open your terminal (macOS: Terminal or iTerm | Windows: PowerShell or WSL | Linux: any terminal) and run:

```bash
# Check VS Code is installed
code --version

# Check Git is installed (recommended)
git --version
```

If VS Code is not in your PATH, open it manually. If Git is missing, download from [git-scm.com](https://git-scm.com).

---

### Step 2 — Install Claude Code (Native Installer)

**macOS / Linux — run in terminal:**
```bash
curl -fsSL https://claude.ai/install.sh | sh
```

**Windows — run in PowerShell (as Administrator):**
```powershell
irm https://claude.ai/install.ps1 | iex
```

**After install — open a NEW terminal window and verify:**
```bash
claude --version
```

> ⚠️ Important: Open a **new** terminal after install. The PATH update only applies to new shell sessions.

**Run the health check:**
```bash
claude doctor
```
This confirms your Node version, network access, and auth state. Resolve any warnings before continuing.

---

### Step 3 — Install the VS Code Extension

1. Open **VS Code**
2. Press `Ctrl+Shift+X` (Windows/Linux) or `Cmd+Shift+X` (Mac) to open Extensions
3. Search for **"Claude Code"**
4. Install the extension published by **Anthropic**
5. Reload VS Code if prompted

You should now see the **✦ Spark icon** in the top-right corner of the editor.

---

### Step 4 — Authenticate

1. Click the **✦ Spark icon** in VS Code (or in the left Activity Bar)
2. A browser window will open — log in with your **Anthropic / Claude account**
3. Authorise Claude Code
4. Return to VS Code — you should see a confirmation that you're logged in

**If using an API key instead:**
```bash
# In terminal, set your API key as an environment variable
export ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxx
```

---

### Step 5 — Create a Sample Project

In your terminal, create a practice folder:

```bash
mkdir claude-code-lab
cd claude-code-lab
git init
```

Create a simple Python file to work with:

```bash
cat > app.py << 'EOF'
# Simple greeting app
def greet(name):
    return "Hello, " + name

def add_numbers(a, b):
    return a + b

if __name__ == "__main__":
    print(greet("World"))
    print(add_numbers(3, 4))
EOF
```

Open the folder in VS Code:
```bash
code .
```

---

### Step 6 — Your First Prompt in VS Code

1. Click the **✦ Spark icon** to open the Claude Code panel
2. Type the following prompt and press **Enter**:

```
Read the file app.py and explain what it does in plain English
```

**Observe:**
- Claude reads the file (you'll see it reference `app.py`)
- It returns a clear explanation without you having to paste any code
- This demonstrates Claude Code's ability to read your project directly

---

### Step 7 — Explore Plan Mode

Plan Mode lets Claude plan a task without executing anything — perfect for reviewing before committing.

**Enable Plan Mode in VS Code settings:**
1. Open Command Palette: `Ctrl+Shift+P` / `Cmd+Shift+P`
2. Type **"Open User Settings (JSON)"**
3. Add this line inside the JSON object:
```json
"claudeCode.initialPermissionMode": "plan"
```

**Now give Claude a more complex task:**
```
Add input validation to the greet function — it should raise a ValueError if name is empty or not a string. Also write a test function for it.
```

**Observe:**
- Claude proposes a plan with specific steps
- It shows **what files it would change** and **what code it would write**
- It does NOT make any changes yet
- You can approve, modify, or cancel the plan

This is the **Discern** stage of the 4D Framework in action — using AI as a collaborator, not just an executor.

---

### Step 8 — Approve and Execute

Once you're satisfied with the plan:

1. Click **"Approve"** (or type `yes` if in terminal mode)
2. Watch Claude apply the changes to `app.py`
3. Review the diff in VS Code's built-in diff viewer

The updated file should include:
- Input validation in `greet()`
- A `test_greet()` function

---

### Step 9 — Use the Integrated Terminal

Try Claude Code from the VS Code integrated terminal:

1. Open terminal: `` Ctrl+` `` (Windows/Linux) or `` Cmd+` `` (Mac)
2. Run:
```bash
claude
```
3. Type this prompt:
```
What is the current state of app.py? Summarise what it does and what tests exist.
```

> Note: The CLI and VS Code extension share the same conversation history. Use `claude --resume` to continue a previous session.

---

### Step 10 — Reflection & Discussion

Answer these questions with your group:

1. **What surprised you** about how Claude Code interacted with your project?
2. **Where in your own workflows** could you apply an agentic coding approach?
3. **When would you use Plan Mode** vs. allowing Claude to act immediately?
4. **What guardrails** would your organisation need before deploying Claude Code to a development team?

---

### Lab Completion Checklist

- [ ] Claude Code installed and `claude --version` returns a version number
- [ ] `claude doctor` shows no errors
- [ ] VS Code extension installed and authenticated
- [ ] First prompt ran successfully (file explanation)
- [ ] Plan Mode enabled and a plan reviewed before execution
- [ ] Changes reviewed in VS Code diff viewer
- [ ] CLI tested in integrated terminal
