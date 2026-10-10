# Talk behavior evaluation

Maintenance evidence for changes to `talk`. Normal invocations do not load this file.

## 2026-08-27: assumption ledger, opt-in document, load-bearing decomposition

Three behavior problems motivated this change. The skill could decide on its own that a decision "needs a durable multi-part contract". It would write a spec the user never asked for. The review surface prescribed six named fields. This caps the document at the template shape rather than the decision shape. Step 2 stated the goal "keep the decision minimal" without a method for choosing what to cut. A tangled ask could return a broad answer. Nobody could check its parts independently.

The governing failure model is assumption capture. An agent meets an unknown. It picks the most plausible reading to keep moving. That reading reaches implementation. Code, tests, and dependent modules then all conform to it. This proves internal consistency. It never proves correctness. Every later exception patches the distance between the assumption and reality. The complexity stops coming from the problem. The countermeasure has to act while the assumption is still one sentence.

### Changes

- Frontmatter declares assumption visibility and load-bearing reduction
- New assumption classification: Confirmed, Open, Assumed, Unexamined
- Gate: no assumption reaches a recommendation unlabeled
- Step 2 became `Cut to the load-bearing decision` with method (find the one decision that determines the others)
- Artifact named **decision record**, not PRD or spec—treats decided and undecided as equals
- File output opt-in only: user must explicitly request

### Result

File output requires user request. Assumptions must be labeled. Load-bearing decomposition method provided. `SKILL.md` ~700 words.

## 2026-09-29: five iterations to dialogue-first simplicity

Motivation: September changes added phase system, five-step trace, batched frontier questions (from grilling), growing from 700 to 1300 words. This turned lightweight dialogue into form-filling ceremony. Five iterations on 2026-09-29 returned to dialogue-first design.

### Iteration 1: remove phase system

**Problem**: five-step trace (`This round: 1. Absorb · Ablation...`), phase labels, batched frontier questions made talk feel like protocol execution. It did not feel like conversation.

**Changes**:
- Removed five-step trace, phase headers, method annotations
- Removed batched Q1/Q2 frontier questions (that's grilling's job)
- Simplified to conversational structure
- Clarified boundary: talk finds assumptions this recommendation depends on; grill explores entire decision space

**Result**: ~1100 words. Structure changed from "execute protocol" to "have conversation".

### Iteration 2: surface ALL assumptions, explicit language rules

**Problem**: "ask 1 key question" contradicts core purpose—any hidden assumption can affect result. Language rules understated. Wayfinder reference obsolete.

**Changes**:
- Step 5 title: "Surface ALL Assumptions" 
- Step 6: ask about all load-bearing assumptions, not just one
- Added Language section with ASD-STE100 rules (short sentences, common words, active voice)
- Removed wayfinder reference (deprecated)
- Restored 6bae3bb structure: 6 clear steps, Discipline, Gates sections

**Result**: ~1150 words. Clear job: find and surface all assumptions before they become code.

### Iteration 3: skill instructions demonstrate required language

**Problem**: skill told models "use simple language" but instructions used complex phrasing. Models imitate what they see more than what they're told.

**Changes**:
- Rewrote entire skill in ASD-STE100 style
- Step 1 "Say what you heard": embody "restate understanding" prompt spirit
- Step 2 "Find the real problem": embody "find core problem" prompt spirit  
- Headers simplified: plain verbs, concrete actions
- Created clarify skill (replaced bro): short, direct, ASD-STE100

**Result**: ~1100 words. Instructions demonstrate the language they require.

### Iteration 4: use专有名词, cut explanation bloat

**Problem**: over-explanation dilutes instruction. Saying "short sentences, common words, active voice" weaker than "use ASD-STE100"—专有名词compress knowledge model already has.

**Changes**:
- Language section: 3 lines. "Use ASD-STE100" instead of expanding all rules
- Method steps: stripped to "do what", removed "why" repetition
- Removed repetitive reminders across sections
- Compacted all sections: output format, multi-turn, discipline, gates, file output
- clarify skill: 4 lines total

**Result**: ~800 words, closer to 6bae3bb's 700. Core method visible, not buried.

### Iteration 5: remove output format template, guide content not structure

**Problem**: markdown template (`**What I understood**: <...>`) causes models to mechanically fill forms in dialogue, losing conversational flow. Format belongs in documents, not conversation.

**Changes**:
- Removed "Output format" section with markdown template
- Added "What to include in your response": lists content elements without prescribing format
- Step 4: changed from "Say: **Decision**: ..." to "Include: what and why, evidence..."—describes content not markdown structure
- Kept format requirements only for file output (ADR)

**Result**: ~800 words. Dialogue gets content guidance + "say it naturally". File output gets structure. Format rigidity only where it belongs.

## Summary

talk evolved from protocol execution back to natural dialogue:
- Core method: 6 clear steps (say what you heard → find real problem → read evidence → recommend → surface all assumptions → ask and wait)
- Language: ASD-STE100 demonstrated throughout, not just described
- Output: content guidance for dialogue, format structure for files only
- Boundary: talk finds assumptions this recommendation depends on; grill explores full decision space
- File output: ADR format, user request only
- ~800 words, close to original 700

The skill guides what to cover, not how to format it. Shows the language it requires instead of describing it. Conversation stays natural and flexible.
