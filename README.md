<p align="center">
  <a href="https://github.com/runapi-ai/grok">
    <h3 align="center">Grok API Skill for RunAPI</h3>
  </a>
</p>

<p align="center">
  Call Grok 4.5 with OpenAI-compatible clients through RunAPI.
</p>

<p align="center">
  <a href="https://runapi.ai/models/grok"><strong>Model Reference</strong></a> |
  <a href="https://github.com/runapi-ai/grok"><strong>Skill Repo</strong></a> |
  <a href="https://runapi.ai/models"><strong>All Models</strong></a>
</p>

<div align="center">

[![skills.sh](https://www.skills.sh/b/runapi-ai/grok)](https://www.skills.sh/runapi-ai/grok/grok)
[![ClawHub](https://img.shields.io/badge/ClawHub-runapi--grok-111827)](https://clawhub.ai/runapi-ai/runapi-grok)
[![License](https://img.shields.io/github/license/runapi-ai/grok)](https://github.com/runapi-ai/grok/blob/main/LICENSE)

</div>
<br/>

Configure the official OpenAI SDK or any compatible client with
`https://runapi.ai/v1`, send `grok-4.5`, and use one RunAPI balance for chat,
reasoning, function tools, structured output, and streaming. This skill teaches
Claude Code, Codex, Gemini CLI, Cursor, and 50+ agents how to wire Grok 4.5
requests through RunAPI.

The canonical agent file is `skills/grok/SKILL.md`.

## Install the skill

```bash
npx skills add runapi-ai/grok -g
```

Or paste this prompt to your AI agent:

```text
Install the grok skill for me:

1. Clone https://github.com/runapi-ai/grok
2. Copy the skills/grok/ directory into your
   user-level skills directory (e.g. ~/.claude/skills/
   for Claude Code, ~/.codex/skills/ for Codex).
3. Verify that SKILL.md is present.
4. Confirm the install path when done.
```

## Use Grok on RunAPI

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_RUNAPI_TOKEN",
    base_url="https://runapi.ai/v1",
)

response = client.chat.completions.create(
    model="grok-4.5",
    messages=[{"role": "user", "content": "Review this rollout plan."}],
    reasoning_effort="high",
)
print(response.choices[0].message.content)
```

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "YOUR_RUNAPI_TOKEN",
  baseURL: "https://runapi.ai/v1",
});

const response = await client.chat.completions.create({
  model: "grok-4.5",
  messages: [{ role: "user", content: "Review this rollout plan." }],
  reasoning_effort: "high",
});
console.log(response.choices[0].message.content);
```

## Protocol compatibility

Grok text models are available through RunAPI's OpenAI-compatible Chat
Completions and Responses interfaces, Anthropic-compatible Messages interface,
and Gemini `contents` interface. RunAPI preserves the exact public model id
`grok-4.5` across all four request protocols; use the protocol already expected
by your application or agent runtime.

Get a RunAPI API Key at <https://runapi.ai/api_keys>.

## Supported Grok models

| Model ID | Notes |
|---|---|
| `grok-4.5` | Chat, coding, reasoning, tools, and structured output |

## References

- Grok API on RunAPI: <https://runapi.ai/models/grok>
- Provider page: <https://runapi.ai/providers/xai>
- Browse all models: <https://runapi.ai/models>

## Agent rules

- Keep API keys in `OPENAI_API_KEY` or a secret manager.
- Use the exact model id `grok-4.5`.
- Stream longer responses with `stream_options.include_usage` enabled.
- Link to <https://runapi.ai/models/grok/4.5> for pricing.

## License

Licensed under the Apache License, Version 2.0.
