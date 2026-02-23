---
layout: post
title:  "Observability Engineering"
date:   2025-12-11 11:00:00 +1000
categories: ["observability", "telemetry", "otel"]
tags: ["observability", "telemetry", "otel"]
preamble: "My approach to observability."
---

Observability is such an interesting topic for me, it is something that I am very passionate about and something that I find a lot of people either get completely wrong, or develop a basic understanding of some concepts and use those concepts as the proverbial hammer to every observability nail.

I'm not saying that my approach is the "one true way", but I've had a lot of success with my approach and I hope that by sharing it you can improve how you approach observability.

## What is Observability Engineering?

I like to try and keep the definition simple, this may not be the canonical definition that the Datadog or Honeycomb folks prefer to use, but I think this definition is simple and accurate enough for our purposes.

_Observability Engineering is the practice of building your systems such that you have the ability and tools necessary to deeply understand how your app is running in as close to real-time as possible and use that data to ask questions of your system and be able to get the answers._

What does that mean exactly? Well: 
- We need to make sure that when we build our systems that we understand how they run and what they are doing when they run.
- Not every system has the power of a LISP or Smalltalk where you can run a debugger or REPL environment on your live production system, so we have to settle for near real-time visibility into our systems.
- It is not possible for us to know beforehand every question we will ask of our collected data before we ask it (we can't know unknown unknowns beforehand), so we need to prepare ourselves as much as is reasonably possible with data so that when we do ask these questions we have the best chance of getting answers.

## Obserability isn't logs, traces and metrics.

While these are the most common types of data gathered from our system, these are simply just abstractions or patterns for viewing or working with the truly important concept in observability, which is events.

Every bit of data that goes into an observability system is an event.

- Logs are events containing some kind of textual description of the event and are usually aggregated by some kind of correlation ID.
- Traces are events demonstrating code paths that have been called and the surrounding data (parameters, return values, surrounding data) and are usually aggregated by some kind of root trace ID.
- Metrics are key/value events which are usually grouped by the key and aggregated via some mathematical function (min, max, sum etc) on the value.

In fact, if you really think about it, if you structure your logs properly, you could make your logs be traces or events simply by having the necessary data and aggregating those events.

- If I emit a log which contains the name of the method/function I just called and I add in some additional context data about parameters etc, and link all of those logs together by a root trace ID, then when I filter by that root trace ID in my log viewer, I have traces.
- If I emit a log which contains a key and some kind of numeric value, then when I view my logs if I group the results by the key and aggregate the value in some way then I have metrics.

## About logs, the most over-abused type of event.

I blame the over-abuse of logs on the fact that for many developers, learning to debug a program comes from shoving print statements all over their code, running it and seeing what gets printed out. When they move into working on things like a web application running in a production cloud environment the equivalent of that is to write a log.

My least favourite type of log is the one which is just a string that concatenates variables into the string value, i.e.

```
"Customer with ID 12345 added Product 54321 to their shopping cart."
```

That may seem helpful at the time when you are working on your local dev machine or when it is in some kind of staging environment, but when you are deep in the middle of an incident and you can't find the relevant log because the string is too hard to search for in your log viewer or there are simply too many logs coming in to find what you are looking for, suddenly this is a whole lot less helpful.

My first bit of advice is that your log messages should be more like events, if I were to rewrite the above example I would do it like so.

```
"Customer added Product to their shopping cart."
```

Or, if you're feeling particularly "event-y".

```
"cart.product.added"
```

Now of course I hear you saying "but how do I know who the customer is and what product they added to their cart!?" And the answer to that is structured logs. Structured logs are a simple concept of rather than just emitting a string, you emit some kind of structured data to your logger, most often JSON (but it doesn't have to be, so long as it is consistently structured and supported by your log platform!). So lets structurise our event into a good log.

```json
{
	"message": "cart.product.added",
	"timestamp": 1765413966891,
	"level": "INFO",
	"data": {
		"cart": {
			"id": 67890
		},
		"customer": { 
			"id": 12345 
		}, 
		"product": {
			"id": 54321 
		} 
	} 
}
```

So now if I want to see all the products that the customer added to their cart, in datadog my search would be:

```
@message:"cart.product.added" @data.customer.id:12345 
```

And then from there I could narrow it down by cart ID:

```
@message:"cart.product.added" @data.customer.id:12345 @data.cart.id: 67890
```

Or, I could even ask which customers have added the specific product.

```
@message:"cart.product.added" @data.product.id: 54321
```

## What data do I include in my structured logs?

In short, highly unique data and lots of it.

Ok, that may be too short of an answer, so the long-form version is data that has high cardinality and high dimensionality. If you don't know what those mean, that's ok because while I know what they mean I can never remember the specific words for them (in fact, I had to google for the specific words while writing this!)

*Cardinality:* _in databases refers to the "uniqueness" of a value. For example, a users UUID is a very high cardinality value as in the context of your data one UUID uniquely identifies an individual user. Conversely, a users full name is a low-cardinality value as it is possible for more than one person to have the exact same first and last name, or a combination thereof. Your structured data should prioritise high-cardinality data as that is the kind of data that makes it easier to drill down and find what you are looking for._

*Dimensionality:* _in databases refers to how "wide" or how much data is contained within a single entry. An entry is considered to have high dimensionality the more data is contained in the entry. In observability we want high-dimensionality for our structured logs because the more data we have available the more questions we can ask our systems and get answers._

Unfortunately, platforms like Datadog make high-dimensionality hard because they place size limits on the total size of the data being sent to datadog and in general recommend that you have no more than 100 key/value pairs in your structured data.

## What about structured data on traces and metrics?

Remember how we talked about how logs, traces and metrics are all events and how you could roll your own traces and metrics out of log events? Well you can add whatever structured data you want to any log, trace or metric you want.