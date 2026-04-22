# Paytm Merchant Travel Desk — Case Study Repo

Voice-first travel booking helpline for Paytm merchants. Portfolio case study
demonstrating 0→1 product thinking, AI architecture, and launch execution. All
artifacts are **markdown documentation** — there is no application code to
build, test, or deploy.

## Folder Map

| Folder | Purpose |
|---|---|
| `01-problem-research/` | User interviews, jobs-to-be-done, competitive analysis |
| `02-solution-design/` | 5-stage call flow, voice-GUI orchestration, conversation scripts, proactive triggers, companion screen design |
| `03-ai-architecture/` | Conversational AI stack (STT → NLU → Dialogue → APIs → TTS), NLU intent examples, fallback architecture (18 failure modes), human handoff logic |
| `04-mvp-specification/` | PRD, scope in-vs-out, pilot design, edge-cases register |
| `05-launch-plan/` | 23 execution artifacts — kickoff, load test, dogfood, go-live gates, legal/compliance, ops dashboard, training curriculum, week-1 exit, week-7 go/no-go, retrospective |
| `06-impact-projection/` | Business case, 3-year financial model, competitive moat, post-MVP roadmap |
| `07-appendix/` | Travel-fintech fusion thesis, interview prep, references |
| `08-demo/` | **Interactive D3 visualization** of the 5-stage call flow + 18 failure modes (`call-flow-diagram.html`) |

## Read First (if a new session)

1. `README.md` — executive summary + strategic context
2. `04-mvp-specification/prd.md` — the canonical product spec
3. `02-solution-design/5-stage-call-flow.md` — the 100-second call structure
4. `03-ai-architecture/fallback-architecture.md` — 18 failure modes table
5. `05-launch-plan/week-7-go-no-go-memo.md` — pilot result and scale decision

## Writing Conventions

- **Don't invent numbers.** Pilot results, frequencies, revenue projections, and
  SLAs are all anchored in existing artifacts — cite the source file when
  adding anything new.
- **Hindi transliteration** uses Roman script matching existing scripts
  (e.g. "Phir se bataiye?", "Ek second"). Don't switch to Devanagari or mix styles.
- **Tables over prose** when the content is structured (stages, failure modes,
  API SLAs, phase gates).
- **Bold the key metric** in any summary paragraph so the number is scannable.
- **Confidence tags** are used in strategic claims: `[Confidence: High/Medium/Low]`.
  Preserve these when editing.
- **File cross-references** use backticks and relative paths
  (e.g. `` `04-mvp-specification/prd.md` §7 ``).

## The Interactive Demo

`08-demo/call-flow-diagram.html` is a self-contained single-file D3.js v7
visualization. To view it: open the file in any modern browser directly from
disk — no build step, no server. It shows all 5 stages with 18 failure modes
branching off and a clickable recovery-cascade panel per failure.

## Do / Don't

- ✅ Add new artifacts with the same tone, density, and cross-referencing as
  existing ones.
- ✅ Update the README folder table if adding a new top-level folder.
- ✅ Commit on `claude/review-build-plan-ls8GT`; merges to `main` happen via PR
  or explicit user instruction.
- ❌ Don't create new documentation files unless the user explicitly asks.
- ❌ Don't invent pilot numbers, failure frequencies, or financial figures.
- ❌ Don't auto-open PRs without explicit user request.
