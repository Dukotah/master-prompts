# master-prompts

A curated reference library of **real, production system prompts** extracted from leading AI coding agents and assistants — Claude Code, Cursor, Windsurf, v0, Lovable, Codex CLI, Cline, Manus, Perplexity, and more.

> ⚠️ **Read this first — what these files actually are.**
> Each `Prompt.txt` is the *internal system prompt* (the operating manual) of a shipping AI tool. These are **not** prompts you paste into Claude Code to "upgrade" it. Pasting another tool's full system prompt into Claude Code will make it *worse*, not better — it references tools, sandboxes, and UI that don't exist in your environment, and it conflicts with Claude Code's own (newer) built-in prompt.
>
> Use this repo as a **pattern library to study and mine**, then port the good techniques into the things that actually steer your agent: your `CLAUDE.md` files, custom slash commands/skills, and `~/.claude` memory.

## How to actually use this repo

1. **Study the patterns, not the whole files.** The value is in the *techniques* — worked examples, tone rules, verification loops, "follow existing conventions," agentic persistence. See [`BEST-PATTERNS.md`](./BEST-PATTERNS.md) for the distilled, attributed cheat-sheet.
2. **Port good ideas into your own steering files.** Drop the patterns that fit your work into a project `CLAUDE.md`. A ready-to-adapt starter lives in [`templates/CLAUDE.md.example`](./templates/CLAUDE.md.example).
3. **Compare tools.** Want to know how Cursor does codebase search vs. how Claude Code does it? The files are side by side. Great for designing your own custom commands.
4. **Don't copy wholesale.** Most rules are coupled to a specific harness (v0's color/font rules, Cursor's `codebase_search`, Warp's terminal-only constraints). Extract the *principle*, discard the tool-specific scaffolding.

## What's inside

| Category | Tools |
|---|---|
| **Terminal / CLI agents** | Claude Code (Anthropic), Codex CLI (OpenAI), Gemini CLI, Warp.dev |
| **IDE agents** | Cursor (v1.0 → 2.0), Windsurf, Augment, Trae, Junie, Kiro |
| **Web app builders** | v0 (Vercel), Lovable, Bolt, Replit, Same.dev, Emergent |
| **Open source** | Cline, Bolt, Codex CLI, Gemini CLI |
| **Research / browser** | Perplexity, Comet, Manus |

> 📌 **Note on freshness:** these are point-in-time captures (e.g. the Claude Code file is a Sonnet 4 / Aug 2025 version). Tools update their prompts constantly, so treat any specific rule as a *snapshot*, not the current state of that product.

## Files

```
Anthropic/Claude Code/Prompt.txt      Cursor Prompts/ (5 versions)
Open Source/Codex CLI/Prompt.txt      v0/Prompt.txt
Open Source/Cline/Prompt.txt          Lovable/Agent Prompt.txt
Open Source/Bolt/Prompt.txt           Windsurf/Prompt Wave 11.txt
Open Source/Gemini CLI/Prompt.txt     Augment Code/...
Warp.dev/Prompt.txt                   Manus/Prompt.txt
Perplexity/Prompt.txt                 Comet/System Prompt.txt
Replit, Same.dev, Trae, Junie, Kiro, Emergent
```

## See also

- [`BEST-PATTERNS.md`](./BEST-PATTERNS.md) — the distilled techniques worth stealing, with attribution.
- [`templates/CLAUDE.md.example`](./templates/CLAUDE.md.example) — a drop-in starter built from the strongest patterns.

---
*This is a study/reference collection. System prompts are credited to their respective tools; all are publicly circulated captures used here for educational comparison.*
