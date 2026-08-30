+++
title = "The protocol gave up."
description = "On 28 July, MCP answered its critics by amputating itself. What the specification removes, why, and what it really changes in the MCP versus CLI debate."
slug = "2026-08-30-the-protocol-gave-up"
date = 2026-08-30T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["AI", "Development"]
+++

On 28 July, while France was buckling under its third heatwave of the year, the Model Context Protocol shipped a new version of its specification. And of course... it was the least of our worries.

But now that the temperature has dropped, it was time for me to look at what it actually amounted to. And I was not disappointed.

This revision comes eight months after the previous one. Set against a protocol born in November 2024, that is more than a third of its entire existence. So this is not a rushed release, it is the longest piece of work in its history, and it has one characteristic I like: what shipped is not addition, it is removal.

Since February, the dominant view fitted in a single sentence: MCP is needlessly heavy, give the agent a terminal and let it work. The protocol has just answered its critics by amputating itself.

## A specification that removes

Here is what the revision deletes:

- **sessions and the `Mcp-Session-Id` header**, which let the server remember you from one call to the next
- **the `initialize` / `initialized` handshake**, the opening negotiation where client and server agreed once and for all on what each could do
- **`ping`**, which checked that the link was still up
- **`logging/setLevel`**, through which the client set the server's verbosity, now stated request by request
- **stream resumability**, which let you recover a response cut off mid-flight

And here is what it deprecates, with twelve months' notice:

- **Roots**, through which the server learned which directories on your machine it was allowed to look at. Now to be passed as a tool parameter
- **Sampling**, through which the server asked the client's model to think on its behalf. To be replaced by a direct call to a provider's API
- **Logging**, the channel through which the server pushed its logs back into the client. To be replaced by `stderr` or by OpenTelemetry

Read line by line, the list looks like housekeeping. Read as a whole, it says something else.

Every one of those features assumed a server that had the run of your house. A server that remembers you, that knows your file system, that drives your logs, and that can put your model to work on its own account. Taken together they described a protocol that wanted to be the agent's runtime environment. **That is the ambition that has just been abandoned.**

What replaces it is deliberately mundane. Every request now describes itself: protocol version, client capabilities and client identity travel in a `_meta` field, on every single call. And a server that needs memory between two calls mints an explicit handle, which the model passes back as an ordinary argument. The specification justifies the choice in a sentence I find honest: they found this worked better than session state hidden in the transport.

## Because it would not deploy

The reason is not theoretical. It is operational, and it is frankly a little humiliating for a protocol that saw itself as modern.

Before, a remote MCP server required sticky sessions, a session store shared across instances, and a gateway able to open the JSON body of requests to work out what to do with them. In other words, everything we have spent fifteen years learning not to do.

Since 28 July, any request can land on any instance. A plain round-robin load balancer is enough. The server runs serverless, scales to zero when nobody needs it, and a container restart becomes invisible to the client.

Take the concrete case, because that is where this plays out. Running an MCP server on a Lambda was already possible before July, but by workaround: either an SDK flag that switched session negotiation off, or a DynamoDB store queried on every invocation to fake a continuity the protocol demanded and Lambda cannot provide. Since 28 July, statelessness is no longer a workaround, it is the specification, and a function behind a Function URL becomes a compliant deployment. With one reservation: the new notification channel is a response held open, and that will never fit inside a Lambda.

The detail I find most revealing is elsewhere, in the `Mcp-Method` and `Mcp-Name` headers, now mandatory.

Until now, everything travelled through the same URL and the same verb. Listing the tools, calling the most harmless one, or triggering the one that writes to your database, your gateway saw the same thing: a `POST /mcp`. To tell them apart you had to open the JSON body of every request, which is expensive, brittle, and which nobody did.

Now the intent is written on the envelope. Your rate limiter can treat `tools/call` differently from `tools/list`, your firewall can refuse a tool by name, your billing can count per tool. In plain terms, the people who operate the thing get their usual instruments back.

{{< schema-entetes >}}

This is the first version of MCP written for the people who run it, not for the people who demo it. It has a price, mind you: the break in compatibility is deliberate, a server on 2026-07-28 will not talk to an older client. The counterpart is a formal deprecation policy, with a twelve-month minimum before anything is withdrawn. It is unglamorous, and it is exactly what was missing.

## The debate was not won, it was moved

The criticism aimed at MCP was first about the cost in context, and it was well founded.

On 11 March, at the Ask 2026 conference, Denis Yarats, co-founder and chief technology officer of Perplexity, announced that his company was moving away from it internally. One figure travelled everywhere: up to 72% of the context window consumed before the user's first message. The concrete case behind the percentage says more. Three servers, GitHub, Playwright and an IDE integration, accounted for 143,000 tokens of tool definitions on a model that accepts 200,000. The agent started its day with three quarters of its memory already taken up by the instruction manual.

Anthropic had measured the same thing on its side, bringing 150,000 tokens down to 2,000 by rewriting the calls as code. A 98.7% reduction.

The July specification answers that grievance at protocol level: freshness hints and cache scope on tool listings, deterministic ordering so that upstream prompt caches actually hit. That is the boring answer, and it was necessary.

The interesting answer is dated 31 July.

Simon Willison, co-creator of Django and one of the most widely read observers on the subject, wrote on 4 November 2025 that he no longer used MCP at all with his coding agents, command line utilities doing the job perfectly well. Three days after the specification, he published a post whose title says it all: stateless MCP has recaptured his interest. And his reason is not scalability. He writes that giving an agent a shell with internet access is fraught with risk, whereas MCP tools are easier to audit and to control, and simple enough that small models running on a laptop can still drive them.

The man who said "give the agent a terminal" comes back explaining that the terminal is precisely the dangerous part.

Kenneth Sinder, who built Notion's hosted MCP server, had made the same point differently back in June: a good CLI is MCP with extra steps. Rebuild progressive disclosure, consistent authentication, token-dense output and the skills layer that tells the model how to drive it, and you have not escaped MCP. You have rebuilt it, without the schema, without the registry, without the shared vocabulary, and you will maintain it alone.

The difference that survives the exercise comes down to one thing: **credential custody**. With MCP, the harness holds the credentials and attaches them outside the model's channel. The model produces arguments, reads results, and never sees the key. With a raw CLI, the key sits in the environment the model is manipulating itself.

And the thing it does not fix has to be said straight away, because Sinder has the decency to say it himself: MCP does not protect you from prompt injection. The injection surface is the content coming back from the tool, not the transport carrying it. A model allowed to call an authenticated tool can still be manipulated into misusing it. What shrinks is the credential exfiltration surface. That is not nothing, it is not everything, and conflating the two is exactly the shortcut that sells solutions.

## What the protocol will never decide for you

In that same post of 31 July, Willison announces `datasette-mcp`, a server exposing three tools. The third one is called `execute_sql()`.

On the same day, the same person explains that MCP is more auditable than a shell, and publishes a server that exposes raw SQL.

This is not a contradiction, and I am not going to treat it as one (all the more so as I make no claim to be in the same league). It is the clearest demonstration you could ask for: **the protocol decides nothing.** It moves the question from the transport to the perimeter, and the perimeter is something nobody will write for you.

The move to statelessness makes that work more visible, in fact, because it turns three protocol problems into ordinary engineering problems.

Idempotency, first. Stream resumability is gone: a broken stream loses the request in flight, and the client must reissue it with a new identifier. The protocol defines no idempotency key. If your tool writes anything, deduplication is on you.

State, next. The handle you mint to remember one call to the next now travels through the model's context, so it is visible to anything that can influence that context. It has to be signed, bound to the caller, and given an expiry. That is token hygiene, a craft we already know.

The third point is the one that interests me most. The new multi round-trip pattern lets a server answer `input_required` instead of a result, listing what it is missing. A confirmation, a parameter, a decision. Asking before acting becomes a first-class kind of response, not a session hack.

That is exactly what I have been arguing for months. You do not grant autonomy to an agent, you design its path. The protocol has just supplied the standardised stopping point that was missing to do it properly.

## What a protocol gains by shrinking

Antoine de Saint-Exupéry wrote this in 1939, in *Wind, Sand and Stars*, about the aeroplane:

> It seems that perfection is attained not when there is nothing more to add, but when there is nothing more to take away.

MCP has stopped wanting to be the agent's operating system. It has remained its contract.

What survives the amputation is nothing spectacular: a tool surface that is described, typed, discoverable, cacheable, loggable, and identical for everyone. A commenter quoted by InfoQ summed up the sceptical reading in a question worth asking: at that point, has it not just become an API again?

Almost. And that is rather good news.

Because the question was never which pipe the call travels down. It has always been: who holds the key, what am I willing to be asked, and who can prove what was asked. A terminal answers all three badly. A well-designed API answers them, provided somebody has done the work.

The specification of 28 July did not win the debate. It conceded it. It handed back everything it was reproached for, and what remains stands up.

A protocol willing to shrink is a protocol that intends to last.

## Sources

- [The MCP 2026-07-28 specification](https://blog.modelcontextprotocol.io/posts/2026-07-28/) and its [changelog](https://modelcontextprotocol.io/specification/2026-07-28/changelog) — the exact list of removals, deprecations and the feature lifecycle policy.
- [Simon Willison, "Stateless MCP has recaptured my interest"](https://simonwillison.net/2026/Jul/31/stateless-mcp/) — the 31 July reversal, and `datasette-mcp`.
- [Kenneth Sinder, "The battle for context: why MCP vs. CLI is the wrong fight"](https://ksinder.medium.com/the-battle-for-context-why-mcp-vs-cli-is-the-wrong-fight-3c0cb63849a4) — the CLI as MCP with extra steps, and the distinction between credential custody and injection.
- [InfoQ — "MCP Goes Stateless, and Developers Ask Whether That Just Makes it an API Again"](https://www.infoq.com/news/2026/08/mcp-stateless-gateway/) — the sceptical reading, and header-based routing seen from the gateways.
- [Repello AI — "MCP vs CLI: What Perplexity's Move Actually Means for AI Security Teams"](https://repello.ai/blog/mcp-vs-cli) — the Denis Yarats announcement and the 143,000 token breakdown.
- [Anthropic — "Code execution with MCP"](https://www.anthropic.com/engineering/code-execution-with-mcp) — the 150,000 tokens brought down to 2,000.
- [Google Developers Blog — scaling with stateless MCP](https://developers.googleblog.com/scaling-ai-agent-infrastructure-with-the-mcp-stateless-updates/) — the operations side, load balancing and serverless.
- [Equixly — "Stateless MCP: what the 2026-07-28 specification changes for security"](https://equixly.com/blog/2026/08/05/stateless-mcp/) — the missing idempotency, and state becoming visible in the model's context.
