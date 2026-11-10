# Model Tiers: One Agent, Different Models for Different Jobs

One agent does not mean one model for everything. The agent routes each job to the right tier. The rule is simple: fast and cheap while a person is waiting, bigger and slower only when the job needs it.

This matters most for a live agent, for example an agent listening on a call. A big model in the live loop will lag the conversation and cost a lot per minute, for no better extraction.

## Transcription is not an LLM job

Use a dedicated speech to text service for listening and transcribing, for example Deepgram Nova. An LLM listening to raw audio is slow, expensive, and worse at transcription. The speech service hands clean text to the LLM.

## Live, while a person is waiting: fast tier

This tier extracts facts as they are spoken, for example lane, weight, dates, and rate talk, and checks them against an API or historical data.

- Claude: Haiku.
- Muse: Spark, the fast tier, not Wonder.
- OpenAI: the mini tier, for example GPT-4o mini.

Use this tier live because the job is extraction, not deep reasoning. The person on the other end cannot wait 5 to 10 seconds for a big model to think.

## After the fact, or when stuck: capable tier

Use this tier only for the hard parts, off the live clock. Post-call summary, rate synthesis, flagging a weird lane, or deciding what to do when the fast tier is unsure.

- Claude: Sonnet.
- Muse: Wonder, the most capable tier.
- OpenAI: the full tier, for example GPT-4o.

Do not put the capable tier in the live loop as the default. Escalate to it only when the fast tier flags low confidence or a conflict.

## Rates and facts never come from the LLM

A rate, a price, or a fact must come from the API or the historical data, never from the LLM guessing. The LLM reads the source, extracts what was said, and compares the two. If the source is missing, the agent says it does not know, and asks the Owner or the human on the call.

## How to pick

1. Start with the fast tier live and the capable tier after the fact.
2. Test it on real calls, not just sample audio. Measure two things: how long the agent takes to respond, and how often extraction is wrong.
3. If extraction is wrong, fix the prompt and the source data first. Do not jump to a bigger model.
4. Move a job to the capable tier only when the fast tier keeps failing that exact job, and the cost of the failure is higher than the cost of the slower model.
