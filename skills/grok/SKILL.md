---
name: grok
description: Call Grok 4.3, 4.5, and Grok 4.20 non-reasoning through RunAPI with the official OpenAI SDK or compatible clients.
metadata:
  openclaw:
    homepage: https://runapi.ai/models/grok
    primaryEnv: OPENAI_API_KEY
    requires:
      env:
      - OPENAI_API_KEY
      - OPENAI_BASE_URL
    envVars:
    - name: OPENAI_API_KEY
      required: true
      description: RunAPI API key used by OpenAI-compatible Grok clients.
    - name: OPENAI_BASE_URL
      required: true
      description: Set to https://runapi.ai/v1 for Grok on RunAPI.
---

# Grok on RunAPI

Use the official OpenAI SDK or any OpenAI-compatible HTTP client. Set the base
URL to `https://runapi.ai/v1`. Use Chat Completions with `grok-4.5`, or use
Responses with `grok-4.3` or `grok-4.5`.

Use `grok-4.20-0309-non-reasoning` for text-only Chat, Responses, or Messages
requests. It is stateless and supports only basic system/user/assistant history;
tools, structured output, images, caching, and continuation fields are rejected.

## Setup

```dotenv
OPENAI_API_KEY=YOUR_RUNAPI_TOKEN
OPENAI_BASE_URL=https://runapi.ai/v1
```

Get a RunAPI API Key at <https://runapi.ai/api_keys>.

## Chat Completions

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_RUNAPI_TOKEN",
    base_url="https://runapi.ai/v1",
)

response = client.chat.completions.create(
    model="grok-4.5",
    messages=[{"role": "user", "content": "Review this implementation plan."}],
    reasoning_effort="high",
)
print(response.choices[0].message.content)
print(response.usage)
```

```typescript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "YOUR_RUNAPI_TOKEN",
  baseURL: "https://runapi.ai/v1",
});

const response = await client.chat.completions.create({
  model: "grok-4.5",
  messages: [{ role: "user", content: "Review this implementation plan." }],
  reasoning_effort: "high",
});
console.log(response.choices[0].message.content);
```

## Responses

Use `client.responses.create` with either exact model id. This synchronous
example requests strict structured output:

```python
response = client.responses.create(
    model="grok-4.3",
    input="Return the highest rollout risk and its severity.",
    reasoning={"effort": "high"},
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

Responses function tools use a flat tool definition. For a streaming request:

```python
stream = client.responses.create(
    model="grok-4.5",
    input="What is the weather in Shanghai?",
    stream=True,
    tools=[{
        "type": "function",
        "name": "get_weather",
        "description": "Get current weather for a city",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {
                    "type": "string",
                },
            },
            "required": ["city"],
            "additionalProperties": False,
        },
    }],
    tool_choice="auto",
)

for event in stream:
    if event.type == "response.function_call_arguments.delta":
        print(event.delta, end="", flush=True)
```

Do not treat a Responses stream as complete until it includes
`response.completed`, terminal `usage`, and `[DONE]`.

## Streaming

Request terminal usage with `stream_options.include_usage` so the final event
contains the authoritative token counts before `[DONE]`.

```python
stream = client.chat.completions.create(
    model="grok-4.5",
    messages=[{"role": "user", "content": "Draft a concise rollout checklist."}],
    reasoning_effort="medium",
    stream=True,
    stream_options={"include_usage": True},
)

for chunk in stream:
    if chunk.choices and chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
    if chunk.usage:
        print(f"\n{chunk.usage}")
```

## Function Tools

```python
response = client.chat.completions.create(
    model="grok-4.5",
    messages=[{"role": "user", "content": "What is the weather in Shanghai?"}],
    tools=[{
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get current weather for a city",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                    },
                },
                "required": ["city"],
                "additionalProperties": False,
            },
        },
    }],
)

for tool_call in response.choices[0].message.tool_calls or []:
    print(tool_call.function.name, tool_call.function.arguments)
```

## Structured Output

```python
response = client.chat.completions.create(
    model="grok-4.5",
    messages=[{"role": "user", "content": "Return a risk assessment."}],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "risk_assessment",
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
        },
    },
)
print(response.choices[0].message.content)
```

## Protocol compatibility

Grok text models are available through RunAPI's OpenAI-compatible Chat
Completions and Responses interfaces, Anthropic-compatible Messages interface,
and Gemini `contents` interface. RunAPI preserves the exact public model ids
`grok-4.3` and `grok-4.5` across request protocols; use the protocol already
expected by your application or agent runtime.

## Request Rules

- Use the exact model id `grok-4.3` or `grok-4.5`; do not send historical Grok aliases.
- `reasoning_effort` accepts `low`, `medium`, or `high`; omit it to use the model default.
- Do not send `presence_penalty`, `frequency_penalty`, or `stop` with this reasoning model.
- Preserve terminal `usage` when storing or displaying billing evidence.

## Supported models

| Model ID | Use when |
|---|---|
| `grok-4.20-0309-non-reasoning` | — |
| `grok-4.5` | Chat, coding, reasoning, tools, and structured output |
| `grok-4.3` | Responses, reasoning, function tools, and structured output |

## References

- Grok 4.3 overview and pricing: https://runapi.ai/models/grok/4.3.md
- Grok 4.5 overview and pricing: https://runapi.ai/models/grok/4.5.md
- Provider page: https://runapi.ai/providers/xai.md
- Catalog: https://runapi.ai/models.md

## Agent rules

- Keep API keys in `OPENAI_API_KEY`, `RUNAPI_TOKEN`, or a secret manager.
- Default Grok 4.5 chat integrations to `POST /v1/chat/completions`; use `POST /v1/responses` for Grok 4.3 and Responses-native workflows.
- Prefer streaming for longer responses and request terminal usage.
- Link to the catalog page for pricing instead of copying price values.
