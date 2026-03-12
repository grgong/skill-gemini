# skill-gemini

Enable any coding agent to invoke the [Google Gemini CLI](https://github.com/google-gemini/gemini-cli) for code analysis, refactoring, and automated editing.

## Prerequisites

- Gemini CLI installed and on `PATH` (`npm install -g @google/gemini-cli`)
- Valid credentials (`GEMINI_API_KEY` or Google Cloud auth)
- Verify with `gemini --version`

## Installation

### Via [skills CLI](https://github.com/vercel-labs/skills) (Recommended)

```bash
npx skills add grgong/skill-gemini
```

### Manual

Copy the `skill-gemini` directory into your agent's skills folder:

```bash
# Claude Code
cp -r skill-gemini ~/.claude/skills/gemini

# Codex / universal
cp -r skill-gemini ~/.agents/skills/gemini
```

## Usage

Ask your agent to delegate a task to Gemini:

```
Use gemini to analyze this repository and suggest improvements.
```

The skill will guide the agent through model selection, sandbox configuration, and command assembly.

See [SKILL.md](SKILL.md) for full operational instructions.

## License

MIT
