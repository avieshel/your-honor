# Your Honor

> Your Honor, this service holds read-write credentials to a production payments
> database, serves every user record over HTTP with no authentication, and has not been
> asked a single question by its own tests.

A skill that puts your code on the stand, drafts the strongest honest defense you could
offer, and then shows you how that defense holds up.

## What this is not

**This cannot tell you your code is secure, compliant, or safe. Nothing can, and this
does not.**

It is not an audit. It is not a certification. It is not a penetration test, and it is
never a substitute for one. It is a **smoke alarm**, not a fire inspection.

An empty docket means the trial saw what it was shown. It did not attack your system, it
did not test your assumptions, and it did not check you against a single standard.
**Silence is never evidence of safety.** If you need real assurance, that comes from a
qualified human, a real audit, and the standard you are actually accountable to.

It is also not legal advice. The courtroom is a device for making an audit memorable, not
a claim about liability, privilege, or anything else a courtroom implies.

## Why it works this way

Security reviews get skimmed. Nobody finishes a list of forty findings the week they ship.

So this inverts the job. Instead of telling you what's wrong and hoping you care, it puts
your own words in your mouth and lets you hear how they sound:

> **Your Honor,** the host is on an internal network and is not routable from the internet.
> The blast radius is engineers and CI jobs with repository read access, not the public.

That defense is *true*. It also doesn't answer the question — the password is in every
clone, every CI cache, and every backup that has ever contained this directory. So the
court rules it **WEAK**, and the weakness is the finding.

**The strength of your defense is the signal.** A bad defense means a real bug. A good
defense means you're fine. That's the whole trick, and it means a scary-sounding finding
you can answer is not a problem — while a nitpick you *can't* answer is the thing that
should stop your deploy.

This is also why the skill refuses to write a weak defense on purpose. A strawman defense
would let you win an argument you were losing.

## Install

```bash
git clone https://github.com/avieshel/your-honor
cp -r your-honor/skills/your-honor ~/.agents/skills/
```

`~/.agents/skills/` is the cross-runtime location recognized by OpenCode, Codex, Copilot
CLI, and Gemini CLI. If your runtime uses a different path, copy the
`skills/your-honor/` directory there — the contents are portable and have no dependencies.

## Use it

```
your honor
your honor, I've been reviewing the auth on this service and I want to know if I can defend it
put this codebase on trial before I ship it
```

Or invoke it explicitly with the skill name. Point it at a repo, a directory, a diff, or
a branch.

## What you get

```
Your Honor, [the state of the codebase, and the charge that matters most]

CHARGE 1 — [short title]
  What the code did and what could follow from it
  Exhibit A — path/file.js:12-18
  Your testimony:  "Your Honor, [your strongest honest defense]"
  Ruling: WEAK — [why the defense does or does not survive]

...

Where the defense holds     <- the things that look dangerous and are not
Outside the record          <- dangerous things found beyond what you asked about
Ruling                      <- counts, and the one thing to fix first
```

Three verdicts, and they describe **the argument**, never the severity:

| Verdict | The test | Meaning |
|---|---|---|
| **HOLDS** | You can point to a specific file, test, or constraint that makes the bad outcome not happen | **This one charge** is answered. Not a security guarantee. |
| **WEAK** | The defense is true but does not cover the harm | This **is** the finding — the argument is the symptom |
| **COLLAPSES** | There is no honest defense | Indefensible. Fix before shipping. |

A critical finding whose defense HOLDS is safer than a nitpick whose defense COLLAPSES.
If the verdict is driven by blast radius instead, you've built a to-do list — not a smoke
test.

## It takes evidence from your tools

If a tool has already entered evidence, the court doesn't need to re-derive it:

| Tool | Charge it supports |
|---|---|
| Dependabot, Renovate, `npm audit`, `pip-audit` | Delivering outdated packages, known CVEs |
| gitleaks, trufflehog, secret scanners | Credentials committed to the repository |
| Semgrep, CodeQL, SAST | Injection, taint flows, unsafe sinks |
| Snyk, GitHub advisories | Vulnerabilities with named fix versions |
| CI output, lint reports, failing tests | Prior testimony you are ignoring |

## Design rules

These are what keep it honest, and they are enforced in the skill rather than left to
taste:

1. **No fabricated charges.** Every charge cites a `file:line`. A finding you cannot cite
   is a guess, and guesses get labeled as guesses. Trust is the only currency this skill
   has, and a false finding costs more than a missed one.
2. **No strawman defenses.** The defense is the honest best one the developer could
   actually give.
3. **Dismissal is a valid result.** Most code is fine. A skill that always finds something
   is a broken skill.
4. **Evidence beats reading.** If it can be booted, probed, or executed, it should be — and
   the response becomes the exhibit.
5. **Never wink at the joke.** The humor is in taking a real engineering problem seriously
   in legal terms. Substance underneath, framing on top.

## Contents

```
skills/your-honor/
  SKILL.md                 the trial: charges, testimony, verdicts, ruling
  references/sweep.md      what to sweep for, and how to tell a landmine from a fire
```

## Contributing

The skill was written test-first: baseline runs against a deliberately broken codebase
recorded where an agent drops the framing or refuses the role, and the skill was written
against those specific failures. It is verified against both a vulnerable sample and a
clean one — a skill that only works on broken code is a skill that invents findings.

Please keep both properties. If you find a prompt where it manufactures charges against
good code or starts implying the code is secure, that's a bug worth an issue.

## License

MIT