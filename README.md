# LLM Cost-Optimization & OpenRouter Playbook

A practical guide for everyday AI usage: balancing terminal CLIs, browser interfaces, and model tiering for near-100% cost efficiency.

---

## 1. Quick Architecture & Configuration

### A. Terminal CLI (`aichat`)
- **Config File**: `~/.config/aichat/config.yaml`
- **Environment Key**: `~/.config/aichat/.env` (`chmod 600`)
- **Default Model**: `anthropic/claude-haiku-5.5` ($0.10 / 1M prompt tokens)
- **Escalation Model**: `anthropic/claude-sonnet-5.5` ($2.00 / 1M prompt tokens)

#### Config Example (`~/.config/aichat/config.yaml`):
```yaml
model: openrouter:anthropic/claude-haiku-5.5
highlight: true
save: true

clients:
  - type: openai-compatible
    name: openrouter
    api_base: https://openrouter.ai/api/v1
    api_auth:
      header: Authorization
      value: "Bearer $OPENROUTER_API_KEY"
    models:
      - name: anthropic/claude-haiku-5.5
        max_input_tokens: 200000
        max_output_tokens: 2048
      - name: anthropic/claude-sonnet-5.5
        max_input_tokens: 200000
        max_output_tokens: 4096
```

### B. Desktop / Browser UI (Chatbox)
- **Install (Arch Linux / Omarchy)**:
  ```bash
  yay -S chatbox-bin
  ```
- **Web Interface**: [web.chatboxai.app](https://web.chatboxai.app) or [openrouter.ai/chat](https://openrouter.ai/chat)
- **Settings**:
  - Provider: `OpenRouter`
  - Model: `anthropic/claude-haiku-5.5`

---

## 2. Daily CLI Cheat Sheet

| Task | Command | Why / Cost |
| :--- | :--- | :--- |
| **Everyday Q&A** | `aichat "How do I extract a .tar.zst file?"` | Uses Claude Haiku (~$0.10/1M tokens) |
| **Interactive REPL** | `aichat` (type `.exit` to quit) | Fast scratchpad chat |
| **Pipe Command Output** | `git diff \| aichat "Write a concise commit message"` | Haiku processes diffs cheaply |
| **Log Filtering** | `cat error.log \| tail -n 50 \| aichat "Diagnose this"` | Avoids feeding 10k lines of noise |
| **Hard Coding / Reasoning** | `aichat -m openrouter:anthropic/claude-sonnet-5.5 "Refactor..."` | Explicitly escalate to Sonnet |

---

## 3. The 3-Tier Model Strategy

| Tier | Models | Approx. Price | Use Cases |
| :--- | :--- | :--- | :--- |
| **Tier 1: Daily Workhorse (90% of tasks)** | `anthropic/claude-haiku-5.5`<br>`google/gemini-2.5-flash`<br>`deepseek/deepseek-chat` | ~$0.10 / 1M | Syntax reminders, bash commands, regex, translations, summaries, quick commits, initial drafts. |
| **Tier 2: Deep Work & Refactoring (10% of tasks)** | `anthropic/claude-sonnet-5.5`<br>`openai/gpt-4o` | ~$2.00–$3.00 / 1M | Multi-file architecture, tricky bugs/race conditions, security/auth logic, database migrations. |
| **Tier 3: Extreme Reasoning / Research (<1%)** | `anthropic/claude-opus`<br>`openai/o1` | ~$15.00+ / 1M | Math proofs, novel algorithm design, high-stakes formal publications. |

---

## 4. Key Competitors to Haiku in the Budget Tier

- **Google Gemini 2.5 Flash**: Huge context window (1M+ tokens), multimodal (videos/PDFs).
- **DeepSeek V3**: Near-Sonnet benchmarks at budget prices.
- **OpenAI GPT-4o Mini**: Great for strict structured JSON and tool calling.

---

## 5. Security Note
Never commit the `.env` file containing your OpenRouter key (`sk-or-v1-...`) to Git! Keep it in `.gitignore`.
