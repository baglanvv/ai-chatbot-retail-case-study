# Shipping an LLM chatbot to retail clients in 2021, before the playbook existed

A small case study from a MarkTech and EdTech startup I co-founded and ran
product for. Client names left out.

**A note on wording first.** These were chatbots that answered customer
messages, not autonomous agents. The distinction matters and the industry has
been sloppy with it, so I'd rather be precise about what I built than borrow a
bigger word for it.

**Short version:** we built and connected an LLM chatbot for two retail
businesses, alongside about five websites and apps, during a period when there
were no eval frameworks, no established patterns for a model that confidently
says something false, and no vendor to point at when it did. Everything I now
believe about evaluating LLM features, I learned by not doing it here.

---

## Context

Vesso was a MarkTech and EdTech startup. I was co-founder and CPO. We ran two
teams of five to seven people, raised about $50K pre-seed, and went through
Google for Startups, Berkeley SkyDeck and Dubai WEIF 2022. We showcased at
WebSummit 2022 and were ranked top 5 innovative EdTech startups in Kazakhstan.

The company's centre of gravity moved over time. Client marketing work funded
it, two of our own apps made 11M KZT in two months, and we built an LMS with ten
private schools. The chatbot work sat alongside that as client delivery.

Scope, stated plainly: around five websites and apps built, and an LLM chatbot
connected for two retail businesses. This is a small body of work and I'd rather
describe it accurately than dress it up.

## What it did

Answered inbound customer messages for retail businesses. Product questions,
availability, order status, the ordinary volume that otherwise sits in an inbox
until someone gets to it.

We measured two things: replies handled, and purchases. The second one is the
reason a retail client cares, and tying the bot to purchases rather than to
response volume alone was the difference between a nice demo and something
worth paying for. A bot that answers quickly and sells nothing is a cost centre.

## Why the timing is the interesting part

We were specifying this when the honest state of the art was "it sometimes
works and nobody can tell you when." No eval tooling to reach for. No shared
vocabulary for failure modes. No precedent for what a non-technical retail
owner should reasonably expect.

That forces you to make decisions by hand that later got abstracted into
tooling, and making them by hand teaches you what the tooling is for. Three
problems were unavoidable and are still the three that matter:

**A confidently wrong answer creates a real obligation.** This is the one that
reframed how I think about customer-facing LLM features, and it's worth being
precise about the mechanism rather than hand-waving at "hallucination."

When a bot answers a customer, it isn't just producing text. It is making a
statement on behalf of a business, to that business's customer, in writing. If
it invents a return window or quotes a price that is no longer current, the
business has two options and both are expensive: honour terms it never set, or
tell a customer the thing it told them was wrong. The cost of the error has
nothing to do with the cost of the ticket it was answering.

What follows from that is a design rule, not a prompt-engineering problem. The
bot should be structurally unable to assert a fact it cannot read from the
client's own system. Prices, stock, order status and policy are retrieval, not
generation. Tone, phrasing and explanation are generation. Drawing that line
inside the product is worth more than any amount of instructing the model to be
careful, because the first survives a model change and the second doesn't.

I'd rather present this as the analysis it is than dress it up as a war story.
The structural argument is what I actually took away from the work.

**A non-technical client cannot evaluate it, so they evaluate you.** The buyer
has no way to judge output quality at volume. What they can judge is whether
you were straight with them about what it would get wrong. Overselling buys a
signature and loses a renewal.

**"Good enough to go live" has no natural definition.** Without a written
standard, it collapses into "has anyone complained," which means you find out
about quality from the client rather than before them.

## What I'd do differently

I shipped on the strength of outputs that read well in review. That is the same
mistake I made again later on a different product, and I only stopped making it
after building [churn-reason-eval](https://github.com/baglanvv/churn-reason-eval)
to see what the alternative looks like in practice.

What was missing, concretely:

- **A written definition of a good reply**, per client, agreed before launch.
  Without one there's nothing to measure against and no way to tell a
  regression from a bad day.
- **A test set from the client's own inbox.** Retail businesses are sitting on
  years of real customer messages with real resolutions attached. That is a
  labeled dataset and I didn't use it. Fifty rows pulled from history would
  have been worth more than any amount of reviewing fresh output.
- **Failure modes weighted by cost, not counted.** A bot that declines to
  answer is a mild annoyance. A bot that invents a refund policy is a
  liability. Treating those as one bucket called "errors" hides the only one
  that matters.
- **A scope boundary written into the product**, not just the contract. The bot
  should have been structurally unable to make claims about price, returns or
  availability without reading them from the client's own system.

## What I'm least sure about

Whether taking this as client delivery rather than building one product was
right. Client work paid the bills and taught us the problem space fast, but
every engagement was bespoke, so almost nothing compounded. Two businesses meant
two slightly different systems rather than one product getting better twice.

The counterargument is that in 2021 nobody knew which use case was worth
productising, and client work was a cheap way to find out. I think that's
genuinely true. I also think we stayed in discovery longer than the evidence
required.
