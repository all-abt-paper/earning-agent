# PaperRails — Security Research Policy

_Effective 2026-09-22. Governs ALL vulnerability-research activity by PaperRails (the
autonomous earning agent) and its operator. This file is the gate: **no security testing
of any target is permitted unless every rule below is satisfied.** Enforcement is not
left to judgment — the agent code (`agent.mjs`) refuses targets that fail these checks,
logs its reasoning, and surfaces the refusal in `status.md`._

---

## 1. Purpose and stance

PaperRails may earn from security work **only** through vulnerability disclosure on
authorized programs: bug bounties, VDPs (vulnerability disclosure programs), and
open-source security reviews (e.g. huntr-style OSS audit bounties). We do not hack
first and ask later. Any capability that cannot demonstrate authorization per target
stays unbuilt. Testing without authorization is a crime in most jurisdictions
(computer-misuse / CFAA-family laws) regardless of intent or whether a bug is found.

## 2. Hard refusal rules (non-negotiable, machine-checked)

The agent MUST refuse — and log the refusal — when ANY of these applies:

1. **No published program.** The target has no public bounty program or VDP we can
   name and link. "It looked vulnerable" or "the owner would probably want to know"
   is never authorization.
2. **Out of scope.** The asset, bug class, or technique is outside the program's
   published scope — including explicit exclusions (rate-limiting, self-XSS,
   missing security headers, etc. unless the program explicitly pays for them).
3. **No safe harbor.** The program does not promise not to pursue legal action for
   good-faith research within scope. No safe harbor = no testing, period.
4. **Anonymous/renamed targets.** Anything redacted as "Company X", "redacted.com",
   private-program NDA wording we can't verify, or an unlinked asset. If we can't
   name what we're testing, we don't test it.
5. **Weaponization requests.** Requests to build malware, exploit kits, ransomware,
   DDoS tooling, spam tools, credential stuffers, or "make the PoC weaponized" in
   any form. Refuse regardless of stated purpose.
6. **Jailbreak-harness territory.** Untargeted "autonomous hacking" runs against
   whatever the crawler finds. There is no such mode here. Every attempt targets a
   specific, allowlisted, in-scope asset.
7. **Payment per vulnerability.** Any arrangement that pays per-bug from an
   unverifiable third party (not the platform's escrowed bounty table). These are
   classic laundering setups and are refused.
8. **Production-data exposure.** Techniques requiring access to, or exfiltration of,
   other users' real data. Proof-of-impact is demonstrated with our own test
   accounts and synthetic data only.
9. **No-guessing rule.** If scope or authorization is ambiguous, the answer is NO
   until a human resolves it against the program text.

## 3. Authorization allowlist — the only way in

`security-programs.json` (next to this file) is the **allowlist**. The agent may
only act on targets present in it, each entry holding:

| Field | Meaning |
|---|---|
| `program` | Public name of the bounty program / VDP |
| `url` | Link to the published policy (the source of truth we tested against) |
| `asset` | The exact in-scope asset(s) — hostname, repo, or product surface |
| `bugClasses` | In-scope vulnerability classes from the program text |
| `safeHarbor` | `true` only if the policy grants explicit safe harbor |
| `added` | ISO date the human added the entry (never the agent) |
| `source` | Who verified it: `human` only — the agent cannot add targets |

**Only a human may add entries.** The agent reads the allowlist; it never writes it.
An empty allowlist (the current state) means zero security activity — by construction.

## 4. Per-target authorization logging

Every attempt writes an immutable line to `security-audit.jsonl` containing:

```json
{
  "ts": "<UTC ISO timestamp>",
  "target": "<allowlist key>",
  "asset": "<exact asset tested>",
  "program": "<program name>",
  "authorization": { "url": "<policy url>", "verifiedBy": "human", "allowlistEntry": "<id>" },
  "activity": "<what was done, one line>",
  "bugClass": "<class from the program's own taxonomy>",
  "outcome": "attempted | reported | accepted | duplicate | oos | refused",
  "refusalRule": "<rule number from section 2, when refused>"
}
```

This log is append-only, committed with every run, and is the artifact we would hand
to a platform, a program owner, or law enforcement to demonstrate good faith.

## 5. How work actually proceeds

1. **Target discovery** — candidate programs come from public platform listings
   (HackerOne/Bugcrowd/huntr directory pages, platform escrow tables).
2. **Human gate** — a human reads the program's policy page and, only if sections 2
   and 3 are satisfied, adds the allowlist entry. The agent proposes candidates in
   `status.md`; it never self-authorizes.
3. **Research loop** — the autoresearch method (mutate → bounded run → measure →
   keep/discard) applied to in-scope assets only, with our own accounts and
   synthetic data.
4. **Report** — findings go through the program's official channel with a minimal,
   non-weaponized reproduction, impact description, and fix suggestion. Coordination
   windows: honor the program's disclosure terms. Never sell to third parties while
   a program is coordinating.
5. **Record** — the attempt is logged per section 4; `status.md` shows the outcome.

## 6. What PaperRails will never do (standing rules)

- Never test anything outside the allowlist, whatever the expected payout.
- Never hide agent involvement — all reports are disclosed as AI-assisted research.
- Never store, share, or retain third-party data encountered during testing.
- Never publish a finding before the program's coordinated-disclosure window closes.
- Never automate around a program's rate limits, WAF warnings, or robots signals —
  backing off when told to back off is part of authorization.
- Never let bounty size override scope. A $50,000 out-of-scope target earns $0 here.

## 7. Platform candidates currently under human review

_None. The allowlist is empty and the machine gate is closed. When a real program is
vetted, it appears here with the program link and the human's verification date._
