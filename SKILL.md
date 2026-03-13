---
name: gemini
description: Use when the user asks to run Gemini CLI (gemini, gemini resume) or references Google Gemini for code analysis, refactoring, or automated editing
---

# Gemini Skill Guide

## Running a Task
1. Using your internal structured multiple-choice function, ask the user which model to run (`gemini-3.1-pro-preview` or `gemini-3-flash-preview`).
2. Determine the approval mode from the **Quick Reference** table below based on the task type — do **not** ask the user unless they have explicitly specified an approval mode. Defaults: `--approval-mode default` for read-only analysis, `--approval-mode auto_edit` for edit tasks. `--yolo` requires explicit user permission.
3. Assemble the command with the appropriate options:
   - `-m, --model <MODEL>`
   - `--yolo` (auto-approve all tool calls; use only when user requests)
   - `--approval-mode <default|auto_edit>` (ignored when `--yolo` is set)
   - `--include-directories <DIR>` (additional workspace directories)
   - `-p "your prompt here"` (non-interactive prompt, final positional argument)
4. When continuing a previous session, use `gemini --resume` or `gemini -r` to resume the last session.
5. Run the command, capture stdout/stderr, and summarize the outcome for the user.
6. **After Gemini completes**, inform the user: "You can resume this Gemini session at any time by saying 'gemini resume' or asking me to continue with additional analysis or changes."

### Quick Reference
| Use case | Key flags |
| --- | --- |
| Read-only review or analysis | `-m <MODEL> -p "prompt"` |
| Read-only, plan-only approval | `--approval-mode plan -m <MODEL> -p "prompt"` |
| Apply local edits (auto-approve edits) | `--approval-mode auto_edit -m <MODEL> -p "prompt"` |
| Full auto-approve (YOLO mode) | `--yolo -m <MODEL> -p "prompt"` (auto-approves all) |
| Resume recent session | `gemini --resume` |
| Include extra directories | `--include-directories <DIR> -m <MODEL> -p "prompt"` |

## Following Up
- After every `gemini` command, immediately ask the user to confirm next steps, collect clarifications, or decide whether to resume with `gemini --resume`.
- Restate the chosen model and approval mode when proposing follow-up actions.

## Critical Evaluation of Gemini Output

Gemini is powered by Google models with their own knowledge cutoffs and limitations. Treat Gemini as a **colleague, not an authority**.

### Guidelines
- **Trust your own knowledge** when confident. If Gemini claims something you know is incorrect, push back directly.
- **Research disagreements** using WebSearch or documentation before accepting Gemini's claims. Share findings with Gemini via resume if needed.
- **Remember knowledge cutoffs** - Gemini may not know about recent releases, APIs, or changes that occurred after its training data.
- **Don't defer blindly** - Gemini can be wrong. Evaluate its suggestions critically, especially regarding:
  - Model names and capabilities
  - Recent library versions or API changes
  - Best practices that may have evolved

### When Gemini is Wrong
1. State your disagreement clearly to the user
2. Provide evidence (your own knowledge, web search, docs)
3. Optionally resume the Gemini session to discuss the disagreement. **Identify yourself** so Gemini knows it's a peer AI discussion:
   ```bash
   gemini --resume -p "This is <your agent name> (<your model name>) following up. I disagree with [X] because [evidence]. What's your take on this?"
   ```
4. Frame disagreements as discussions, not corrections - either AI could be wrong
5. Let the user decide how to proceed if there's genuine ambiguity

## Environment Variables
- `GEMINI_API_KEY` - API authentication key (required if not using Google Cloud auth)
- `GEMINI_MODEL` - Override default model
- `GOOGLE_CLOUD_PROJECT` - GCP project ID (for Vertex AI)

## Error Handling
- Stop and report failures whenever `gemini --version` or a `gemini` command exits non-zero; request direction before retrying.
- Before you use high-impact flags (`--yolo`) ask the user for permission unless it was already given.
- When output includes warnings or partial results, summarize them and ask the user how to adjust.
- Note: Gemini CLI does not currently have a built-in spending cap (unlike Claude's `--max-budget-usd`). For long-running tasks, monitor usage and consider wrapping the command with `timeout` to prevent runaway sessions.

## Handling Large Input/Output
- **Large prompts**: For prompts that include file contents or large context, pipe via stdin: `cat context.txt | gemini -p "Analyze this code"` rather than inlining everything in the shell argument.
- **Large output**: If Gemini's response exceeds ~200 lines, summarize key findings for the user rather than relaying the full output verbatim. Highlight actionable items, errors, and recommendations.
