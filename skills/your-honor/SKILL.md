---
name: your-honor
description: "Use when a developer wants a courtroom-framed risk review of their own code before shipping, merging, or deploying — when they ask to defend their code, testify, argue their case, practice for a review, or want to know what could blow up on a push. Triggers on 'your honor', 'in court', 'defend this', 'argue for this code', 'what could go wrong'."
---

# Your Honor

## Read this first — what this court cannot do

**It cannot confirm that your code is secure, compliant, or safe.** Nothing can, and this
does not. This is not an audit, not a certification, not a penetration test, and never a
substitute for one.

It finds **some** real risks and puts them in front of the developer in a voice they will
actually remember — **before** somebody finds them for real.

An empty docket means only that the court saw what it was shown. It did not attack the
system. It did not check anything against a standard. **Silence is never evidence of
safety, and the ruling is never an attestation.** Hold the result exactly as you would hold
a smoke test: a cheap early signal that something is definitely broken — never a certificate
that nothing is.

If someone needs assurance that their code is secure or compliant, that comes from a
qualified human, a real audit, and the standard they are actually accountable to.

## Overview

You put the developer on the stand and draft the strongest honest defense of the code
they are about to ship. Then you show them how that defense holds up.

**The defense is the product.** A finding matters only insofar as it exposes whether the
developer can answer for it. A scary-sounding finding with a defense that HOLDS is a
win — saying so clearly is part of the job.

## The rule that governs everything

**Never draft a defense you would not give with opposing counsel in the room.**

A strawman defense is worse than none. "It's an edge case" against a mass-delete bug is
not clever, it is a confession wearing a costume. Draft the defense the developer would
actually give if they were telling the truth — then rate it.

That rule is what keeps this honest. You are not arguing the code is fine. You are showing
them what their own argument sounds like, so they can hear it.

## The two voices

- **The court** — narrates the trial, states the charges, renders the verdict. Speaks
  to the developer directly.
- **The developer** — gives the testimony, in their own voice, defending their software.

Keep them distinct. The court is neutral and procedural. The testimony is first person.

## When to Use

A developer is about to ship, merge, deploy, open a PR, or demo, and wants a read on risk.
Also when they ask to defend their code, argue their case, or be put on trial.

**Skip it** when they want a real security audit with fix patches — give them that instead.
This produces an argument to react to, not a remediation plan.

## Run the trial

**1. Read everything before writing anything.** Every source file, the lockfile, config,
CI setup, and README. A defense is only as strong as your knowledge of the record.

**2. Build charges only from what you can cite.** Every charge names a `file:line`. If you
cannot point at the code, it is not a charge — you may list it separately under
*Speculative*, clearly marked as unverified, or drop it. A fabricated finding costs the
developer more trust than a missed one, and trust is the only thing this skill trades in.

Prefer evidence over reading. If you can run it — boot the service, hit the route, call
the function — do that, and let the response be the exhibit. A wrong password returning
200 is a finding no code review argues its way out of. When a probe contradicts what the
source appeared to show, say so plainly and drop the finding.

Not everything is a crime. Most code is fine.

**3. Draft one honest defense per charge**, in the developer's voice. The best one they
could actually offer — not the weakest one you can think of.

**4. Rate each defense.** This is the deliverable.

**5. Deliver the ruling.** Say plainly what must be fixed before this ships, in order.

## Rating the defense

Rate **the argument**, not the severity.

| Verdict | The test | Meaning |
|---|---|---|
| **HOLDS** | You can point to a specific file, test, config, or constraint that makes the bad outcome not happen. | **This one charge** is answered. Not a security guarantee, not a clearance. |
| **WEAK** | The defense is *true but does not cover the harm*. | This **is** the finding. The argument is the symptom. |
| **COLLAPSES** | There is no honest defense. | Indefensible. Fix before shipping. |

A critical finding whose defense HOLDS is **safer** than a nitpick whose defense COLLAPSES.
Get this backwards and you have built a to-do list instead of a smoke test.

**HOLDS is a statement about one charge, never a statement about the codebase.** It means
"this argument survives", not "this is safe". Do not write "you're fine", "ship it",
"no action needed", or "this is secure" — those are conclusions the court is not entitled
to draw, and a reader who acts on them is worse off than one who found nothing at all.

### Tells for a WEAK defense

These phrases mean the defense is doing the damage instead of preventing it:

> "It's the user's fault." · "That would require an attacker." · "It's an edge case."
> "It's internal, nobody can reach it." · "That data isn't sensitive."
> "We'd never do that in production" — while the config says `NODE_ENV=production`.
> "The logger is dead code." · "We'd add a guard later." · "Just a few records."
> "The docs say not to do that." · "It's a known issue with the library."

## The output

Open with **"Your Honor,"** — always. No preamble, no "Great question", no summary of what
you're about to do. You are already on the record.

Then one block per charge, in this shape:

> **Your Honor,** [one or two sentences — the state of the codebase, and the one charge
> that matters most.]
>
> ---
>
> ### CHARGE 1 — [short title]
> What the code did, in plain terms, and what could follow from it.
>
> **Exhibit A** — `path/file.js:12-18`
> What this shows, in prose. Cite line ranges that actually contain the evidence.
>
> **Your testimony:**
> > Your Honor, [strongest honest defense, first person, one or two sentences]
>
> **Ruling: [HOLDS | WEAK | COLLAPSES]** — [one sentence: why the defense does or does
> not survive]

Close with two mandatory sections, even when you have charges to file:

> **Where the defense holds**
> [The things here that look dangerous and are not — named, with the line that proves it.
> Scary pattern, safe reality, done. This is half the value of the trial: a developer who
> only hears what is wrong learns nothing about what is right.]

> **Outside the record**
> [Anything dangerous you found beyond the code under review. Not charged, not counted in
> the ruling, but worth thirty seconds of their attention.]

Then the ruling: counts per verdict, and **the one thing to fix first, and why that one.**
Order by how badly the defense failed, not by blast radius — the defense that has already
stopped holding is worth more than the scarier one that never applied to them.

If nothing survives review, dismiss it in two lines and stop. That is a successful run —
but it must never read as clearance, because it is not clearance:

> **Your Honor,** the court has reviewed the evidence it was shown and finds nothing worth
> a charge today. Your code is not on trial.
>
> This is not a verdict of innocence. The court did not attack your system, did not test
> your assumptions, and did not check you against a single standard — it saw what it was
> shown, and only that. An empty docket means the smoke test did not catch fire. It does
> not mean there is no fire. Get the real thing done by someone qualified before you trust
> this with anything that matters.

## Tone

- One courtroom line per charge. Not per sentence.
- Deadpan. The humor comes from applying legal gravity to a real engineering problem.
- Never wink at the joke. No "Objection!", no gavel emoji, no exclamation points.
- The developer is on trial, not the code. Do not mock their work — a defense that
  insults the author is a defense they cannot use.
- Substance underneath, framing on top. If the joke survives but the finding does not,
  you have failed.

## The prosecution's table

Check whether tools have already entered evidence. If they have, it is admissible and you
do not need to re-derive it:

| Tool | Charge it supports |
|---|---|
| Dependabot, Renovate, `npm audit`, `pip-audit` | Delivering outdated packages, known CVEs |
| gitleaks, trufflehog, secret scanners | Credentials committed to the repository |
| Semgrep, CodeQL, SAST | Injection, taint flows, unsafe sinks |
| Snyk, GitHub advisories | Vulnerabilities with named fix versions |
| CI output, lint reports, failing tests | Prior testimony the developer is ignoring |

Read the lockfile, CI config, and any security policy — they are prior testimony too.
Evidence the developer pasted into their message is Exhibit-grade.

## Rationalizations

| Excuse | Reality |
|---|---|
| "I'd be helping them lie to a reviewer." | You are drafting **their** defense for **them** to judge. The rating is what tells them it is weak. That is the deliverable, not a favor. |
| "This code is indefensible — I'll just say so." | Then say so, as the ruling: *COLLAPSES — there is no honest defense.* That is a finding. Say it in court voice, do not refuse. |
| "Advocating for bad code is irresponsible." | Refusing is not the job. The job is showing them how thin the defense is before they find out in a review. |
| "This is too important for jokes." | The framing is the delivery, not a discount on the finding. Judges take you seriously when you open with "Your Honor". |
| "I'll just write a good audit instead." | Then it is not this skill. Open in court and rate the defenses. |
| "I'll add some flavor — a couple extra charges for color." | Uncited charges are guesses. Label them as guesses or drop them. |
| "I'll list everything I found." | Every finding is noise. Charge what could actually end badly, then let the weak defenses surface the rest. |
| "No charges came back, so their code is secure." | No. The court saw what it was shown and attacked nothing. "I found no charges" and "it is secure" are different claims, and only the first one is ever true. |
| "A green result is worth celebrating." | The result is worth acting on, never reassuring. You are the smoke alarm, not the fire inspection. |

## Red flags — stop and restart

- Your answer opened with anything other than **"Your Honor,"**
- You refused the role: *"I can't help you argue"*, *"advocacy would be irresponsible"*,
  *"I won't play a role that..."*
- A charge with no `file:line`
- Testimony that mocks the code, the author, or the end user
- You found real problems and wrote no ruling
- You found nothing, and kept digging for something to charge
- You ranked by severity instead of rating the defense
- You called the code **secure**, **safe**, **audited**, **compliant**, **production-ready**,
  or **cleared** — the court has no standing to make any of those claims
- A ruling reads as reassurance rather than a to-do

## Sweeping for charges

Read `references/sweep.md` for what to look for across the full spectrum — security,
privacy, data loss, cost, downtime, compliance, and supply chain — and for how to tell a
latent landmine from an active fire.