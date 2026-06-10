---
layout: post
title: "Tired of Parsing LLM Responses? There's a Better Way"
description: Every major LLM gives you a way to define exactly what comes back. Here's why you should be using it.
date: 10-06-2026
categories: [AI, Chat Completion, LLM, Tool Calling]
tags: [llm, tools, structuredoutput, functioncalling]
---

![hero image](/assets/tools.png)

Every major LLM gives you a way to define exactly what comes back. Here's why you should be using it.

---

## The Assumption

You're building a flow that calls an LLM. You need structured data back — a category, a score, a decision. So you do what feels right. You add a line to the system prompt:

> *"Always respond in JSON format."*

You test it. It works. You move on.

Then in production, a slightly different input comes through. And the model responds with:

```
Sure! Here's the JSON you requested:

```json
{
  "category": "Billing",
  "priority": "High",
  "summary": "Customer was charged twice for the same subscription."
}
```


Your JSON parser crashes. Your flow breaks. The model was helpful — just not in the way you needed.

## Why This Happens With Every LLM

This isn't a quirk of one model or one provider. OpenAI, Gemini, Claude, Mistral — they all do this.

LLMs are trained to be helpful to humans. When a human asks for JSON in a chat, a friendly sentence before the code block *is* the helpful thing. The model isn't misbehaving. It just doesn't know the difference between a human reading its response in a chat window and a system trying to parse it downstream.

The system prompt instruction helps, but it's a nudge, not a rule. The model will follow it most of the time. Edge cases, unusual inputs, longer conversations, and suddenly the response looks different. You're not in control of the format. You're just hoping.

So people patch it. Strip the code fences. Trim the preamble. Handle the cases where the model says "Certainly!" before the JSON. It works until it doesn't, and then you're debugging production at an inconvenient hour.

Every major LLM provider ships a proper solution for this. It's called **tools**.


## What Are Tools?

Most LLM APIs support a feature called **tools** — also referred to as *function calling* depending on the provider. The concept is simple.

Instead of asking the model to *write something*, you give it a named function with a defined schema and ask it to *call that function* with the right values. You're not asking for a response. You're handing the model a form and asking it to fill it in.
The key addition alongside the tool definition is forcing the model to always use it:

- Anthropic: `"tool_choice": { "type": "tool", "name": "classify_ticket" }`
- OpenAI: `"tool_choice": { "type": "function", "function": { "name": "classify_ticket" } }`
- Gemini: `"tool_config": { "function_calling_config": { "mode": "ANY" } }`

Without this, the model can still decide to answer in text if it feels the question doesn't warrant a tool call. With it, the output shape is guaranteed every single time.



## Seeing It in Action
Here is a complete API call for the support ticket example. You define the tool, force the model to use it via `tool_choice`, and pass the ticket text as the user message.

**Request:**

​```json
{
  "model": "claude-haiku-4-5-20251001",
  "max_tokens": 256,
  "tools": [
    {
      "name": "classify_ticket",
      "description": "Classify a customer support ticket",
      "input_schema": {
        "type": "object",
        "properties": {
          "category": {
            "type": "string",
            "description": "Ticket category — e.g. Billing, Technical, Account"
          },
          "priority": {
            "type": "string",
            "description": "Priority level — Low, Medium, or High"
          },
          "summary": {
            "type": "string",
            "description": "One sentence summary of the issue"
          }
        },
        "required": ["category", "priority", "summary"]
      }
    }
  ],
  "tool_choice": { "type": "tool", "name": "classify_ticket" },
  "messages": [
    {
      "role": "user",
      "content": "Classify the following support ticket.\n\nTicket: I have been charged twice for the same subscription this month. I checked my bank statement and there are two identical transactions from your company on the 3rd and the 4th. Please refund the duplicate charge immediately."
    }
  ]
}
​```

**Response:**

​```json
{
  "content": [
    {
      "type": "tool_use",
      "id": "toolu_02XyZaBc",
      "name": "classify_ticket",
      "input": {
        "category": "Billing",
        "priority": "High",
        "summary": "Customer was charged twice for the same subscription and is requesting a refund for the duplicate transaction."
      }
    }
  ],
  "stop_reason": "tool_use"
}
​```

No preamble, no fences, no "Sure! Here's your answer." Just `content[0].input` with the three fields — ready to use directly in the next step of your flow. Notice `stop_reason` is `tool_use` and not `end_turn` — that's how you know the model fulfilled the request through the tool rather than writing a text response.

---


> Telling the model to return JSON is a request. Defining a tool is a contract.

## Tools Don't Stop Here

Tools are not just about shaping what the model returns. That's what we used them for here, and it solves the problem well. But tools were originally built for something bigger: letting the model decide when to call an external function, what inputs to pass, and hand that instruction back to you to execute, so your code can fetch data, trigger actions, and feed results back into the conversation. Think of it as the model directing a workflow, not just answering a question. We'll get into it in the next one.

---

*Sandeep Angara*  
*June 10, 2026*
