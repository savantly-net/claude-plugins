# Writing style for generated artifacts

Every file these skills write to disk follows the rules below: requirements, acceptance
criteria, architecture and design docs, ADRs, epics, spikes, `STATUS.md`, history
entries, `HANDBOOK.md`, and the `NEXT_SESSION.md` pointers.

The rules are the enforceable core of **ASD-STE100 Simplified Technical English**. That
specification also carries an approved dictionary of about 900 words, which a session
cannot check against from here — so **do not claim ASD-STE100 conformance in the
artifacts themselves**. The dictionary is not applied. The writing rules are.

## Why

A requirement is read years later, by someone who was not in the room, often not in
their first language, and increasingly by a machine. Every synonym, every stacked noun
phrase, and every 40-word sentence is a place where two readers can leave with two
different specifications. STE exists because ambiguity in maintenance documentation
kills people; the same discipline in a requirements table just costs a rebuild, which
is reason enough.

## The rules

**Sentences**
- One topic per sentence. If it needs "and" plus a comma, it is two sentences.
- Instructions and procedures: **20 words or fewer**. Descriptive text: **25 or fewer**.
- Six sentences maximum per paragraph.
- Present tense by default. Simple past and simple future where needed; no perfect or
  progressive tenses.
- **Active voice.** Name the actor: "the service writes the receipt", not "a receipt is
  written". Passive hides who is responsible, which is the one thing a requirement must
  never do.

**Words**
- **One word, one meaning. One meaning, one word.** Pick a term and repeat it. Never
  vary wording for style — in a spec, a synonym reads as a distinction.
- Use a word as one part of speech only. If `test` is the noun, the verb is `check`.
- Noun clusters: **three words maximum**. "Territory assignment status field" becomes
  "the status of the territory assignment".
- Keep articles. "Update record" and "update the record" do not say the same thing.
- No `-ing` forms where a finite verb or a relative clause works: "the job that runs
  nightly", not "the nightly running job".
- No idiom, metaphor, slang, or undefined jargon. Define an abbreviation at first use
  in each document.
- Specific over vague: give the number, the unit, and the boundary. "About", "some",
  "quickly", and "as needed" are defects in a requirement.

**Structure**
- One obligation per requirement row: `must`, `must not`, or `should`, with the actor
  named. Split anything carrying two.
- One testable condition per acceptance criterion.
- Write procedures as commands, one action per step.
- Use a vertical list when text carries more than two conditions.

## Where these rules do not apply

- **Conversation with the human.** Explaining a trade-off, arguing for a
  recommendation, or presenting a choice needs nuance that 20-word active sentences
  cannot carry. Write those normally.
- **Quoted material.** Gate text, vendor terms, licence language, error strings, and
  anything cited from a source are reproduced verbatim, including their awkwardness.
- **Existing text.** Never reword a decided document to make it compliant. A
  requirement's wording *is* someone's decision, and a silent rewrite of it is
  indistinguishable from a change of meaning. New text follows these rules; old text is
  amended by appending a dated amendment, never by restyling.
- **Names.** Story IDs, file paths, symbols, and product names stay as they are.

## Example

Not this:

> Handling of the various customer-facing notification emails should generally be
> performed asynchronously by the notification subsystem, which will typically dispatch
> them shortly after the relevant triggering event has occurred, so as to avoid
> impacting request latency.

This:

> FR-4.1 — The notification service must send each customer email from a background job.
> The job must start within 30 seconds of its trigger event. The request that causes the
> trigger must not wait for the email.
