---
name: grok
description: Call Grok 4.3, 4.5, and Grok 4.20 non-reasoning through RunAPI using OpenAI-compatible Chat Completions and Responses. Use for text, streaming, tools, structured output, or an existing compatibility client that needs the conditional reference.
documentation: https://runapi.ai/models/grok.md
provider_page: https://runapi.ai/providers/xai.md
catalog: https://runapi.ai/models.md
metadata:
  openclaw:
    homepage: https://runapi.ai/models/grok
    primaryEnv: OPENAI_API_KEY
    requires:
      env: [OPENAI_API_KEY, OPENAI_BASE_URL]
    envVars:
    - {name: OPENAI_API_KEY, required: true, description: RunAPI API key used by OpenAI-compatible Grok clients.}
    - {name: OPENAI_BASE_URL, required: true, description: Set to https://runapi.ai/v1 for Grok on RunAPI.}
---

# Grok on RunAPI

Use OpenAI-compatible endpoints at `https://runapi.ai/v1` as the primary protocol.

## Primary protocol recipe

### Authenticate

Set `OPENAI_API_KEY` to a RunAPI API key and `OPENAI_BASE_URL` to `https://runapi.ai/v1`.

### Send request

```python
from openai import OpenAI
client = OpenAI(api_key="YOUR_RUNAPI_TOKEN", base_url="https://runapi.ai/v1")
response = client.responses.create(
    model="grok-4.5",
    input="Review this rollout plan.",
    reasoning={"effort": "high"},
)
print(response.output_text)
print(response.usage)
```

Use Chat Completions for `grok-4.5` chat workflows and Responses for
`grok-4.3` or `grok-4.5`. For streaming Responses, set `stream=True` and consume
through `response.completed` and `[DONE]`.

### Verify result

Responses require final output, one usage-bearing `response.completed`, and
`[DONE]` for SSE. Chat requires final assistant content, `finish_reason`, and
terminal `usage`.

### Stop boundaries

Correct a rejected shape once using the structured error. Retry transport once
only before any response or Usage and when replay is safe. Record a terminal
error and stop without changing model or protocol. Keep
`grok-4.20-0309-non-reasoning` text-only and stateless; add advanced controls
only when the current RunAPI contract verifies them for the exact model.

## Compatibility protocols

Load [compatibility protocols](references/compatibility-protocols.md) only when an existing client requires Anthropic Messages or Gemini contents.

## Supported models

| Model ID | Use when |
|---|---|
| `grok-4.20-0309-non-reasoning` | Verified text workloads without reasoning controls |
| `grok-4.5` | Current Grok chat and reasoning workloads |
| `grok-4.3` | Stable Grok reasoning workloads |

## References

- <https://runapi.ai/models/grok.md>
- <https://runapi.ai/providers/xai.md>
- <https://runapi.ai/models.md>
