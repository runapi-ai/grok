<p align="center">
  <a href="https://github.com/runapi-ai/grok">
    <h3 align="center">Grok API Skill for RunAPI</h3>
  </a>
</p>

<p align="center">
  Call Grok 4.3, 4.5, and 4.6 with OpenAI-compatible clients through RunAPI.
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
`https://runapi.ai/v1`, send an exact Grok model id, and use one RunAPI balance
for chat, Responses, reasoning, tools, structured output, and streaming. Grok
4.6 is available through Responses; Grok 4.3 and 4.5 retain their existing
compatibility interfaces. This skill teaches Claude Code, Codex, Gemini CLI,
Cursor, and 50+ agents how to wire Grok requests through RunAPI.

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

This Grok 4.6 Responses example uses structured input and requests strict JSON.
No additional `test` field is part of the RunAPI request contract:

```python
response = client.responses.create(
    model="grok-4.6",
    input=[{
        "role": "user",
        "content": [{
            "type": "input_text",
            "text": "Return the highest rollout risk and its severity.",
        }],
    }],
    stream=False,
    reasoning={"effort": "xhigh"},
    text={
        "format": {
            "type": "json_schema",
            "name": "rollout_risk",
            "strict": True,
            "schema": {
                "type": "object",
                "properties": {
                    "risk": {"type": "string"},
                    "severity": {"type": "string", "enum": ["low", "medium", "high"]},
                },
                "required": ["risk", "severity"],
                "additionalProperties": False,
            },
        }
    },
)
print(response.output_text)
print(response.usage)
```

## Protocol compatibility

Grok 4.6 is available through RunAPI's OpenAI-compatible Responses interface.
Grok 4.3 and 4.5 remain available through Chat Completions, Responses,
Anthropic-compatible Messages, and Gemini `contents`. Use the exact protocol
supported by the selected model.

Get a RunAPI API Key at <https://runapi.ai/api_keys>.

## Supported Grok models

| Model ID | Notes |
|---|---|
| `grok-4.20-0309-non-reasoning` | — |
| `grok-4.6` | Responses, image input, four reasoning levels, function tools, and structured output |
| `grok-4.5` | Chat, coding, reasoning, tools, and structured output |
| `grok-4.3` | Reasoning, Responses, function tools, and structured output |

## References

- Grok API on RunAPI: <https://runapi.ai/models/grok>
- Provider page: <https://runapi.ai/providers/xai>
- Browse all models: <https://runapi.ai/models>

## Agent rules

- Keep API keys in `OPENAI_API_KEY` or a secret manager.
- Use the exact model id `grok-4.3`, `grok-4.5`, or `grok-4.6`.
- Add `input_image` with a public `image_url` for Grok 4.6 image understanding.
- Only `function` tools are supported for Grok 4.6. Hosted tools such as `web_search`, `x_search`, `file_search`, `code_interpreter`, `image_generation`, and `mcp` are rejected before execution.
- Do not send `input_file` or state fields such as `previous_response_id` for Grok 4.6.
- For Responses streams, wait for `response.completed`, terminal `usage`, and `[DONE]`.
- Link to the matching catalog variant page for pricing: <https://runapi.ai/models/grok/4.3>, <https://runapi.ai/models/grok/4.5>, or <https://runapi.ai/models/grok/4.6>.

## License

Licensed under the Apache License, Version 2.0.
