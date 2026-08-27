# wayfinder behavior evals

`disable-model-invocation: true`, so there is no route eval — the model can never
select this skill on its own. Evidence here is structural and executable.

## 2026-08-27: port from issue tracker to a local directory

**Change.** The map moved from an issue labelled `wayfinder:map` with child
issues to `docs/wayfinder/<effort-slug>/` holding `map.md`, `tickets/`, and
`resolved/`. Ticket identity moved from a tracker id to a filename.

### The problem the port had to solve

In a tracker the frontier is not stored — it is a query over issue state and
native dependency edges, so it cannot go stale. A local directory has no query
engine, which leaves two options:

| Option | Outcome |
| --- | --- |
| Write the frontier into `map.md` | **Rejected.** Storing a derived value creates a cache. A stale frontier is a future session working a ticket whose premise is not settled — the same assumption-as-fact failure the effort is meant to prevent. |
| Derive it from the filesystem on read | **Chosen.** Truth is the file's location plus its `Blocked by:` line. Nothing is stored, so nothing can go stale. |

### The invariant that makes it work

`Blocked by:` is written once at creation and never maintained, because what
changes on resolution is the blocker's *location*, not the dependency. So
resolving a ticket is one `git mv` and every ticket it blocked becomes takeable
with **no second edit**. Verified end to end:

| Step | FRONTIER | CLAIMED | BLOCKED |
| --- | --- | --- | --- |
| After charting 3 tickets | `notification-types`, `user-table-fit` | — | `push-or-email <- notification-types` |
| Claim one, `mv` the other to `resolved/` | `push-or-email` | `user-table-fit` | — |

`push-or-email` entered the frontier from the move alone. No file other than the
resolved ticket was touched.

### Script checks

`scripts/frontier.py`. The first implementation was `frontier.sh`; the user
rewrote it in Python and the shell version was deleted — two implementations of
one invariant drift. Before deleting, the two were diffed against a 7-ticket
fixture: classification identical, differing only in comma-joined blocker lists,
stderr interleaving, and the bad-slug exit code (1 → 2, which is more correct: a
usage error is not a data error).

Fixture results:

| Case | Expected | Result |
| --- | --- | --- |
| `Blocked by:` blocker in `resolved/` | takeable | ✅ |
| `Blocked by:` blocker in `tickets/` | BLOCKED, names blocker | ✅ |
| Multiple blockers, one open | BLOCKED | ✅ |
| `Blocked by:` absent / `none` / empty | takeable | ✅ |
| `Claimed:` set | CLAIMED, not FRONTIER | ✅ |
| Blocker naming no existing file | DANGLING, **not** FRONTIER | ✅ (fixed mid-review) |
| Empty `tickets/` | all sections `—`, exit 0 | ✅ |
| Directory without `tickets/` | error, exit 1 | ✅ |

The dangling case was wrong on first write: the ticket appeared in both FRONTIER
and DANGLING. An unresolvable blocker name means *whether it is blocked is
unknown*, so presenting it as takeable is exactly the failure mode above. It is
now listed under DANGLING only.

## 2026-08-27: the forgotten-move problem

**Raised by the user:** an agent resolves a ticket, does a lot of work, and
forgets to move the file. Worst case the ticket also still holds `Claimed:`, so
it is in neither FRONTIER nor CLAIMED-as-actionable, and everything it blocks
stalls silently.

Step 4 of *Work through the map* had three actions at the point in a session
where context is most depleted — three chances to forget. Fixed in three layers,
weakest to strongest:

### Layer 1 — order the actions so the losable one is cheap

Answer is appended **before** the `git mv`. An interrupted session then loses a
file move, not the session's reasoning. Already the written order; now it carries
its reason, so an agent doesn't reorder it as a tidy-up.

### Layer 2 — make the inconsistent state loud

A ticket holding `## Answer` while still in `tickets/` is a contradiction:
answered, yet on the frontier. The script reports it under NEEDS FILING, which
takes priority over blocked/claimed classification because it is an integrity
problem rather than a state. Verified:

| Fixture | FRONTIER | NEEDS FILING |
| --- | --- | --- |
| 3 tickets, none answered | `notification-types` | — |
| answer appended, `mv` "forgotten" | — (0) | `notification-types (answered, still in tickets/)` |

The ticket leaves FRONTIER the moment it is answered, so the next session cannot
redo it, and the printed hint names the remaining step (`--resolve <ticket>`).
The same scan treats `## Out of scope` still in `tickets/` as NEEDS FILING —
without that, a scoped-out ticket looked takeable. Two sibling checks were
added on the same scan: UNRECORDED (in `resolved/` with neither `## Answer`
nor `## Out of scope` — moved without recording which) and MAP OUT OF SYNC.

Integrity sections print **before** FRONTIER, each with a `→` line that names
the fix. An agent that takes "the first thing in the output" hits the leftover
instead of a new ticket. Verified:

| Fixture | First section | FRONTIER |
| --- | --- | --- |
| answer appended, `mv` forgotten | NEEDS FILING | does not include that ticket |
| `## Out of scope` still in `tickets/` | NEEDS FILING `(out of scope, still in tickets/)` | does not include that ticket |
| clean chart, nothing recorded | FRONTIER (no integrity block) | the unblocked tickets |

Because the answer lands before the move, being caught here costs one `git mv`.
Nothing is redone.

### Layer 3 — delete the third action

The map's `## Decisions so far` was a hand-written index of `resolved/`. Its link
is derivable from the filename and its gist is authored *at resolution time*,
next to the answer — so writing the index by hand copies information already on
disk, and a copy is a step to forget.

The gist moved into the ticket as the first line of `## Answer` (`Gist: …`), and
both map indexes became generated blocks rebuilt by `--sync-map`.

| | Cost of forgetting |
| --- | --- |
| Before | the gist existed only in the agent's context and died with the session; the map silently lost a line |
| After | the gist is in the ticket; the index is a stale cache, detected on the next run and rebuilt with one command |

Same principle as the frontier itself: don't store a derived value; if one must
land on disk, make it regenerable and self-checking.

Side effect worth stating: the hand-written map is now exactly **Destination,
Notes, Not yet specified** — three sections with no derivable source. "The map
holds only what can't be derived" became literal.

### Layer 3 left one derived value stored — `NN-` removed it

Layer 3's first cut kept route order by *parsing the order already in `map.md`*
and appending new entries after it. That made the map the only home for the
order — a stored derived value, the exact thing the frontier design rejects. Lose
or hand-edit the block and the route silently reorders alphabetically.

Resolved tickets are now `NN-<stem>.md`, numbered **on filing, not on creation**:
in `tickets/` there is no order to record, since a charting session writes several
at once and which gets worked first isn't decided yet. So order became derivable
from the filename, and the order-preservation code was deleted rather than fixed.

| | Where route order lives |
| --- | --- |
| Before | parsed out of `map.md`'s generated block; nowhere else |
| After | the `NN-` prefix on each file; the map sorts on it |

Two consequences that had to be handled:

- **`NN-` is not identity.** Filing renames the file, so `Blocked by:` matching
  compares bare stems — a reference written at charting time still resolves after
  the blocker is numbered. Verified: `Blocked by: notification-types` still
  matches `resolved/01-notification-types.md`.
- **Both indexes share one sequence.** The number is a position in time, not a
  slot in a category, so a gap in **Decisions so far** is the moment something
  was ruled out of scope. Verified: decisions at 01 and 03, out-of-scope at 02.

`--resolve <ticket>` was added so the number is allocated rather than typed:
it validates, allocates `max+1`, moves the file, and syncs the map. It refuses a
ticket holding neither `## Answer` nor `## Out of scope`, which makes the
answer-before-move ordering of layer 1 enforced rather than merely documented.

| Case | Expected | Result |
| --- | --- | --- |
| `--resolve` on a ticket with nothing recorded | refuses, exit 1 | ✅ |
| `--resolve` when `map.md` is missing | refuses **before** the move, exit 1 | ✅ |
| `--resolve` with and without `.md` | both work | ✅ |
| ticket named `2024-review.md` | identity stays `2024-review` after `01-` prefix | ✅ |
| First resolution | `01-`, map indexed | ✅ |
| Second resolution | `02-`, appended after `01` | ✅ |
| Out-of-scope ticket | numbered in the shared sequence, routed to Out-of-scope | ✅ |
| Hand-moved file with no prefix | UNNUMBERED | ✅ |
| Two files claiming `02` | DUPLICATE NUMBERS, names both | ✅ |
| `ls resolved/` | reads as the route walked, in order | ✅ |

UNNUMBERED and DUPLICATE NUMBERS can only arise from moving a file by hand
instead of using `--resolve`.

### `--sync-map` checks

| Case | Expected | Result |
| --- | --- | --- |
| Empty headings, 1 answer + 1 out-of-scope in `resolved/` | one entry indexed under each | ✅ |
| Run twice | `already current`, file unmodified | ✅ |
| Drift report after sync | MAP OUT OF SYNC gone | ✅ |
| `## Answer` vs `## Out of scope` | routed to Decisions vs Out-of-scope respectively | ✅ |
| Second decision added later, alphabetically earlier | route order preserved, new entry appended | ✅ |
| Resolved ticket with no `Gist:` line | indexed as `(no gist recorded)` | ✅ |
| Hand-written sections (`Not yet specified`) | untouched | ✅ |
| Map missing the target heading | exits 1, names the heading | ✅ |
| Map absent entirely | exits 1; read-only run says `write map.md first`, not `--sync-map` | ✅ (hint fixed mid-review) |
| Nothing to index yet | no empty generated block planted | ✅ (changed mid-review) |

Two mid-review corrections: the missing-map hint advised `--sync-map`, which
cannot create a file; and an effort with no out-of-scope tickets got an empty
generated block, which is clutter that reads as a section someone emptied.

### Why the script writes at all

`frontier.py` is read-only by default and writes only under `--sync-map`, only
inside the two delimited blocks. Drift detection is free on the same scan, so the
read path reports the problem and names the fix, and the write stays an explicit
command rather than a side effect of looking.

### Deliberate design choices

- **`Claimed:` is the one stored field**, so it can go stale when a session
  dies. It is kept because the skill expects parallel sessions, and without it
  two sessions deterministically collide on the same first frontier ticket. The
  mitigation is loudness: the script always prints CLAIMED as its own count next
  to FRONTIER, so a stale claim is visible on every run instead of silently
  shrinking what looks takeable.
- **`Not yet specified` now sits above `Decisions so far`.** A tidy document with
  headings invites completeness in a way an issue body did not, and the fog is
  the section a reader is most tempted to fill in to make the map look finished.
- **Two directories, not three.** A `blocked/` directory would make the frontier
  a bare `ls`, but resolution would then have to scan it and promote dependants —
  bookkeeping whose failure mode is a ticket left behind and permanently
  invisible. Reading is cheap; a silently lost ticket is not.
- **`resolved/` means "off the frontier", not "answered".** Out-of-scope tickets
  land there too, recording why in place of an answer, which keeps `tickets/`
  meaning exactly "live work".

### Reference sweep

| Check | Result |
| --- | --- |
| `issue tracker`, `child issue`, `wayfinder:map`, `setup-matt-pocock`, `tracker` in `wayfinder/` | 0 matches |
| `local-markdown tracker` (was a dangling reference — no such doc in repo) | gone; the local layout is now defined in-skill |
| Other skills referencing `wayfinder` | `think/SKILL.md` only, as a routing suggestion with no tracker coupling — unaffected |

`validate_skill.py` reports `Missing agents/interface.yaml`. Pre-existing and
repo-wide: 0 of 49 bam-mode skills have that file, and an untouched skill
(`grilling`) fails identically. Not introduced here.
