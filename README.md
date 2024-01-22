# Dedicated Telegram Sales Representative

This project is a multi-agent sales automation system built on top of the OpenAI Agents SDK. The idea is pretty simple: instead of having a single AI write and send a cold email, we have a small team of specialized agents that collaborate, review each other's work, and handle the entire conversation — from the first outbound email all the way to replying to a prospect's response.

The delivery channel is Telegram, which makes it easy to test without needing to set up an email server or worry about DNS configuration.

---

## How it works

When you run the demo, here is what happens under the hood:

**1. Three writers draft in parallel**

Three sales agents each write a cold email for the same prospect, but with different personalities:
- One writes in a professional, credibility-focused tone
- One is witty and a bit humorous
- One is executive-style: short and straight to the point

They all run at the same time, so there is no waiting.

**2. A picker selects the best draft**

A fourth agent reads all three emails and picks the one most likely to get a response. It puts itself in the shoes of the prospect and chooses without explanation.

**3. A critic reviews the chosen email**

The critic checks for things like a compelling subject line, a clear value proposition, a single call to action, and no leftover placeholders like `[Name]`. If everything looks good, it replies with `APPROVED`. Otherwise it gives specific bullet points on what to fix.

**4. A refiner rewrites if needed**

If the critic had notes, the refiner takes the email and the feedback and rewrites accordingly. This loop can repeat up to three times. Once the critic approves, the process stops.

**5. The final email is sent via Telegram**

The polished email gets sent to the prospect through the Telegram Bot API.

**6. The SDR handles replies**

After the email goes out, the system starts polling Telegram for incoming messages. When the prospect replies, an SDR agent picks up the conversation. It answers questions, proposes a meeting if the person seems interested, and gracefully backs off if they say they are not. The conversation history is stored in a local SQLite database so the agent always has context.

---

## Two orchestration modes

The notebook demonstrates two ways to wire agents together:

**Orchestration by code** — Python controls the entire flow. Each step is explicit: run the writers, pass the output to the picker, send it to the critic, loop if needed. The developer is in full control of when each agent runs and what it receives.

**Orchestration by LLM** — A Sales Manager agent is given a set of tools (the writers, critic, refiner, and a send tool) and decides on its own how to use them. The instructions tell it what the goal is, and it figures out the sequence. This is more flexible but less predictable.

---

## Tech stack

- **OpenAI Agents SDK** — for defining agents, running them, and managing conversation sessions
- **Gemma4:31b via Ollama cloud** — the underlying language model, accessed through an OpenAI-compatible API
- **Telegram Bot API** — for sending messages and long-polling for replies
- **SQLite** — for persisting conversation history between turns
- **Python asyncio** — for running the writer agents in parallel

---

## Setup

1. Create a Telegram bot via `@BotFather` and copy the token
2. Send your bot a message so it registers your chat ID
3. Create an Ollama account and generate an API key
4. Store the following in your environment or Colab Secrets:
   - `TELEGRAM_BOT_TOKEN`
   - `TELEGRAM_CHAT_ID`
   - `OLLAMA_API_KEY`
5. Run the notebook cells in order

---

## Project structure

```
Lab_exercice.ipynb   # Main notebook with all agents and orchestration logic
.env                 # Local environment variables (not for production use)
```
