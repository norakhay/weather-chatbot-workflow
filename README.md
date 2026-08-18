# Lab: Weather Chatbot Workflow

This repository is a lab artifact for a **Weather Chatbot** workflow exported from an agentic platform (Dify-style YAML).

Import `weather_chatbot.yml` into the platform Studio, attach a weather knowledge base, then test in Preview.

## What the bot does

The chatbot answers **weather questions for a named city** in the same language as the user (English or Arabic).

| User message | Expected behavior |
| --- | --- |
| Greeting only (`hi`, `hello`, `مرحبا`) | Short greeting + ask for a city |
| Weather, but no city | Ask for the city; do not invent numbers |
| Weather + city | Short report: city, period, conditions, °C, wind, rain |
| Off-topic (news, law, code, sports) | Refuse; weather-only |

If live weather data is missing, the bot should say numbers are unavailable and **must not invent** temperatures, wind, or rain.

## Workflow graph

```
Start → Knowledge Retrieval (weather) → LLM (LLM-S) → Answer
```

| Node | Role |
| --- | --- |
| **Start** | Receives the user message (`sys.query`). |
| **Knowledge Retrieval** | Searches the **weather** knowledge dataset (`top_k`: 4) and passes results as context. |
| **LLM** | Model **LLM-S** (`agrid/vllm`). Uses the user query plus retrieved snippets. Temperature `0.7`. |
| **Answer** | Sends `{{#llm.text#}}` back to the user. Citations are enabled (`retriever_resource`). |

## Files

- `weather_chatbot.yml` — full app export (`kind: app`, `mode: advanced-chat`, version `0.3.1`)

## How to use this lab

1. Open Studio on the agentic platform.
2. Import / create an app from `weather_chatbot.yml`.
3. Bind **Knowledge Retrieval** to a weather dataset (the export references dataset id `3d41fdc4-caef-4a0a-b8d4-1a1602351d03`; remap this if your workspace uses a different knowledge folder).
4. Confirm the LLM node uses **LLM-S** (or an equivalent chat model in your workspace).
5. Run **Preview** and try:
   - `hey` → should ask for a city
   - `amman` → should return a cited weather summary when knowledge is available

## Notes

- File upload, speech-to-text, and text-to-speech are disabled.
- The app-level system description (weather steps 1–5) is the intended conversation policy. After import, align the LLM node prompt with that policy if the platform still shows a generic support-assistant prompt.
