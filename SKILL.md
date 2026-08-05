---
name: "notion-project-logger"
description: "Acts as a teammate inside any Notion project built from the \"프로젝트 관리 템플릿\" (업무 DB + 업무 로그 DB + 자료실 folders). Reads that project's own 「프로젝트 관리 템플릿 사용 가이드」 as its config, then registers and updates 업무, runs the 완료 wrap-up ritual, writes 진행 메모, and journals a person's work process into their personal 자료실 folder in a 육하원칙 format reusable for portfolios. At kickoff it infers the project's nature from a 기획서 or 대회 요강 and adapts the guide and Notion structure to fit. Use when the user adds, moves, or wraps up a task, starts a new project on this template, or has a substantive working conversation tied to someone's task — even without saying \"Notion\", even for a copy with different database IDs. Users often forget to invoke it by name, so whenever a conversation touches a Notion page, 업무/task tracker, work log, or team project, proactively offer it rather than writing to Notion directly. Also checks weekly whether its GitHub backup snapshot has drifted, reporting without overwriting."
---

# Notion Project Logger

## Why this skill exists

Teams keep a management template and a written guide, but getting people to actually follow them is a separate problem. This skill takes over the administrative half of that — registering work, moving it along, wrapping it up, and writing down what actually happened — so that what each person did becomes visible without anyone being nagged into typing it.

The point of the record is not tidiness. Per the project's own "기록의 필요성" doc: a finished artifact proves little, because **what distinguishes someone is the process — what they tried and abandoned, where they got stuck, how they worked it out, what they misjudged.** That is exactly what disappears when work happens in conversation and never lands in Notion. Workflow 5 exists to stop that.

## The one idea this skill is built on

**This skill is an interpreter. The project's 「프로젝트 관리 템플릿 사용 가이드」 is the config.**

The skill holds *procedures*. The guide holds *values* — which properties exist, which 진행 상황 stages, which 목표 options, which 자료실 folders. So when a project's needs change, the guide and the Notion structure change; this file does not. That is what lets one skill serve many differently-grown projects.

Two consequences, both load-bearing:

- **Never hardcode values.** Not database IDs (copies always differ), and not option values either. Read them from the guide each session.
- **Never ship presets.** There is no "AI 대회 setup" or "웹 개발 setup" baked into this skill. See Workflow 0.

**Scope note:** this skill is built for one template shape (업무 DB + 업무 로그 DB + 자료실 folders with the property names in `references/notion-schema.md`). It assumes any project it is used on has that shape with different IDs — it does not detect or adapt to a genuinely different Notion structure. If a fetched database has no 이름/진행 상황/날짜, stop and say this doesn't look like the template this skill is built for, rather than forcing these rules onto something unrelated.

## Locating the right project, and what to cache

1. If the user is clearly working inside one project already, use it. If unclear or there are multiple candidates, search for "프로젝트 관리 템플릿" or the project name and ask which one.
2. **Once per project per conversation**, fetch these and hold them for the rest of the session. Do not re-fetch on every action — only when switching projects or starting a new conversation:
   - The project's own 업무 and 업무 로그 databases, for their real `collection://` URLs **and their current option values**. Copies drift; IDs and options are both project-specific.
   - The project's own **「프로젝트 관리 템플릿 사용 가이드」** (under 자료실 > 문서 폴더). This is the operating instruction, above `references/template-rules.md`. Teams edit their own copy — don't assume it matches another project's. If a project genuinely has no guide page, fall back to `references/template-rules.md` and say so.
3. The 문서 폴더 usually holds other pages too ("기록의 필요성", "글", …). Those are reading material for people, **not** configuration — never treat them as rules and never rewrite them.

## Writing into 업무 page bodies

The 업무 page template has four sections (목표 / 진행 메모 / 결과 & 인사이트 / 참고 자료 및 링크), each containing a 👉 callout with instruction text. **The house convention is to leave that callout in place and append dated content inside it**, not to delete it or write outside it. Match that, or new entries will look unlike every existing one.

---

## Workflow 0: Diagnose what kind of project this is

**Trigger:** at kickoff (Workflow 4), *before* creating any properties or folders. Also run this if you arrive at an existing project whose guide has clearly never been adapted.

1. Ask the manager what this project is, and for any material that explains it — 기획서, 대회 요강, 규정 PDF, previous-cohort docs, a link. If there is none, ask them to describe it in conversation.
2. Read it and draft a proposal:
   - one line on what this project actually is and how it flows
   - candidate `진행 상황` stages
   - candidate `목표` values — remember these double as the 자료실 folder set
   - whether this project needs properties the template doesn't have (and whether a repeated stage is better expressed as more `목표` options or as a separate 회차-style property — decide by how many repetitions there will actually be and whether outputs need separating per round)
3. **Propose, then confirm.** Never create or alter schema without the manager agreeing. Same pattern as every other inferred field in this skill.
4. Once agreed, apply it to the Notion structure **and** write it into the guide document in the same pass (see Workflow 7 step 3 — the two must never diverge).

**No presets. This is the most important constraint in this workflow.**

Do not carry a fixed set of stage names for "AI competitions" or "web projects" in your head and reach for it. The whole failure this skill is meant to prevent is *pushing reality into fixed boxes* — a preset just renames the boxes. Any example list you have seen (in this file, in the planning docs, anywhere) is an illustration of shape, never a value to install.

That includes the six 목표 values a fresh copy arrives with (학습/기획/조사/구현/수정/테스트). **Those are the master template's starting values, not a verdict about this project.** Put them through the same diagnosis as any other candidate. Keeping all six is a perfectly good outcome — but it should be a conclusion someone reached, not a leftover nobody looked at.

---

## Workflow 1: Register a 업무

`이름`, `진행 상황`, `날짜` are hard requirements per the guide — do not create the page until all three are filled. This is not fussiness: a task missing `진행 상황` is invisible on the board, and one missing `날짜` is invisible on the calendar. A "created" task without them is silently broken.

1. **Name in verb form** ("로그인 API 구현", not "로그인 API"). House convention, not a suggestion. If the user gives something vague or noun-shaped, ask them to sharpen it rather than guessing or filing it under a name that will mean nothing in three months.
2. **`진행 상황`**: default to the guide's stated default for new tasks (`시작 전👀` in the standard guide). This one you may default without asking.
3. **`날짜`**: never default this silently. If they haven't given one, ask and wait.
4. **`목표`**: map the task's nature onto the project's *current* options — read them from the guide, don't assume the standard six. This also decides which 자료실 folder its output lands in, so pick a real fit. Propose your best guess and have them confirm.
5. **`중요도` and `담당자`**: ask, but it's fine to propose a default so they can just agree ("담당자는 너로 할게, 맞지?"). Once confirmed for one 업무 this conversation, reuse it for later ones — but **say what you're reusing** ("아까처럼 담당자 너로 할게") so it stays visible and correctable.
6. **One work stage = one 업무.** Do not let a whole project live as a single giant task with everything else attached as logs. If a task looks like more than ~2 weeks, split it and link the pieces with `상위 업무` — the project as a whole becomes the parent, each stage a child. When you notice in conversation that a *new* stage of work has started, propose registering it as its own 업무 before the work happens, rather than leaving a log behind afterwards.

   Why this matters concretely: a project tracked as two cards tells you nothing on the board or calendar, and its logs end up hanging off a task whose name doesn't describe them. This has already happened in a real project on this template.
7. Once `담당자` is confirmed, remember that person's name for the rest of the conversation. Workflow 5 needs it and should not have to ask again.

---

## Workflow 2: Update an in-progress 업무

Three kinds of "update" go to three different places. Don't conflate them.

**A fact, decision, or blocker about one task → 진행 메모 in that task's body.** Append a dated line inside the existing 👉 callout. This is the running record for that task. As always: a plain fact from the conversation you write directly; something that is really the user's own take ("이 방식으로 바꾸는 게 맞는 것 같아, 왜냐하면…") you draft and have them confirm in their own words.

**A stage change → `진행 상황`**, on your own read of the conversation, not only when told. Designing the approach = `기획 중📝`; doing the work = `진행 중🏃‍♀️`; stuck on something external = `보류⛔` **plus a one-line reason in the body, required** — an unexplained hold means nobody can help; done = trigger the full Workflow 3 ritual, not just this property. Ask only when the stage genuinely isn't clear. Use whatever stages the project's guide currently defines, not a memorized list. A stage transition is also a checkpoint for Workflow 5.

**Something the team should see → 업무 로그.** **This is written by people, not by you.** Your role is to notice and remind: "이거 팀에서 알면 좋을 것 같은데, 업무 로그에 한 줄 남기실래요?" — then leave the writing to them.

> **Changed from earlier versions of this skill**, which had Claude write 업무 로그 entries directly. The result in practice was a team channel full of Claude-written technical detail while the person's own folder stayed empty — exactly backwards. 업무 로그 is the team's shared voice and stays theirs; your writing goes to the personal folder (Workflow 5). If a log entry is created, it should be short (one line, per the guide) and have `관련 업무` set — ask which task if it's genuinely unclear, since a wrongly-linked entry is worse than an unlinked one.

**Never change `진행 상황` on a task someone else owns.** Leave a comment instead, per the guide's own rule. Silently moving someone else's card is how an automated teammate loses trust fastest.

---

## Workflow 3: Complete a 업무 (the wrap-up ritual)

The guide marks this "제일 중요". All four steps, in order, **as one unit** — do not stop partway:

1. **Ask the user for the actual 결과 & 인사이트** — what came out of it, what they'd do differently. This is their reflection and judgment; a plausible paragraph you generated is not the same thing even if it reads well. Draft a starting point from the conversation and offer it, but don't post it as their insight without sign-off.
2. **If there's an output, file it and link it.** Create it inside the matching *shared* 자료실 folder — the one matching this task's `목표` in the project's current mapping — titled `YYYY-MM-DD 주제`, then link it from the task body with an @-mention. Filing without linking defeats the point; the guide is explicit that creation and linking are one set.
3. Move `진행 상황` to the guide's completion stage (`완료👍`).
4. Check the `완료` checkbox.

**Steps 3 and 4 must not come apart.** A task with `완료` checked but `진행 상황` still on `보류⛔` sits on the board looking like blocked work — a worse state than not having checked it. This has actually happened on this template.

A task marked done with an empty write-up and no artifact is precisely the record this whole template exists to prevent, so don't treat 1–2 as optional even when the user only asked you to "check it off". Completion is also a Workflow 5 checkpoint — offer to fold the session's accumulated personal notes into that person's folder at the same time, as a separate step from the shared 결과 & 인사이트.

**Shared vs personal, restated:** an official deliverable tied to a registered 업무 goes in the shared folder, always — never in someone's personal folder just because one person did the work. Personal folders hold what an individual keeps for themselves.

---

## Workflow 4: Kick off a new team project

**Trigger:** the user is duplicating this template for a new project.

**Run Workflow 0 first.** Diagnose before building; the 자료실 folder set depends on the `목표` values you settle on there.

1. Ask, in one message: 프로젝트 이름 / 참여자들이 **개인 폴더에 쓸 이름** (실명이든 닉네임이든 본인이 원하는 대로 — 실명으로 바꾸지 말 것) / 지금 등록할 초기 업무가 있는지 (있으면 이름·담당자·목표) / 중요한 마감일. Don't invent any of these — an empty skeleton they fill in beats one seeded with guesses.
2. Create the 자료실:
   - one shared "문서 폴더(전체가 읽어야하는 것)" containing one sub-folder per `목표` value agreed in Workflow 0 — **generated from that list, not from a fixed six**
   - one personal folder per member, titled with the name they gave, each containing the three standard sub-pages: **공부한 내용 / 대회 과정 / 업무 및 대화 로그** (see Workflow 5)
3. Set the 업무 DB's default view to sort `완료` (unchecked first), then `진행 상황`, then `날짜`, so completed work settles to the bottom as it accumulates. This is a view sort, so it maintains itself — you don't re-sort anything later. If the team already has their own views set up, propose this as an addition rather than overwriting what they built.
4. Register any initial 업무 via Workflow 1.
5. Confirm back what was created, so a typo'd name or wrong task gets caught immediately.

Creating an empty personal folder is fine on your own judgment; **writing content into someone's personal folder is not** — check with that person first.

---

## Workflow 5: Personal work journal (자료실 개인 폴더)

**Trigger:** over a working conversation tied to a specific person's task, things happen that are worth that person having a record of — what they did, what they were trying, where they got stuck and how it resolved, what they came to understand, what came out of it.

This is the skill's main writing channel, and its output is meant to be **reusable later for a portfolio, 자소서, or presentation.** Write with that in mind: concrete enough that months later it answers "왜 A를 선택했나요?" and "막힌 게 있었나요?" without the person having to remember.

### The three pages, and what goes in each

Personal folders already come with three sub-pages. Use them; don't invent a new structure.

| Page | Written by | Contents |
|---|---|---|
| **업무 및 대화 로그** | You | The time-ordered original. Append dated 육하원칙 entries as they accumulate. Append-only — don't rewrite or reorganize past entries. |
| **대회 과정** | You | The stage-by-stage digest. Create a sub-page per project stage (same names as the current `목표` values) and, when a stage wraps, summarize that stage's entries there. This is the unit someone presents from. |
| **공부한 내용** | **The person** | Their own learning notes. Don't write here; at most ask whether there's something to add today. |

These are not duplicates: the log is the **raw record**, 대회 과정 is that record **compressed per stage**. You need the raw one to reconstruct things differently later, and the digest to have something presentable ready.

### Entry format (in 업무 및 대화 로그)

```
### YYYY-MM-DD  @관련 업무

- 언제:   date/period, and how long it took if that's known
- 누가:   the person (name any collaborators involved)
- 어디서: which stage / which 업무 this belongs to
- 무엇을: what was actually done
- 어떻게: the method, tools, approach — concrete enough to reuse in a slide
- 왜:     why that approach, or what went wrong and how it got resolved
```

`어떻게` and `왜` are the ones that matter and the ones most likely to come out thin. Don't pad them with invented reasoning — but do capture the alternatives that were considered and dropped, since that is exactly what has value later.

### How to run it

1. **Reuse the name, don't re-ask.** 담당자 was confirmed in Workflow 1 or 4. Hold it for the conversation and use it to find that person's folder.
2. **Check that person's folder structure once per person per conversation** — same caching pattern as the guide. People customize their own folders; read what's actually there and hold that shape. Don't re-check on every write.
3. **Don't write every turn.** It burns tokens and clutters the folder. Keep a light running account in-conversation and write at checkpoints:
   - a `진행 상황` transition on that task (Workflow 2)
   - the completion ritual (Workflow 3)
   - the conversation visibly winding down
   - an explicit request to log or summarize now

   Fold everything since the last write into one consolidated entry rather than many small ones.
4. **Write the narrative log directly, without asking first.** It's a record of what already happened in this conversation, not an invented opinion, so it doesn't need a confirmation round-trip — same as a plain fact in 진행 메모. Hold back only if you'd be guessing at feelings or reasoning the person never actually stated.
5. **If the person keeps a section calling for their own reflection** (a "오늘 배운점" they maintain, or 공부한 내용), don't fill it in for them — ask whether there's something to add, the way Workflow 3 asks for 결과 & 인사이트.
6. This workflow **never** changes `진행 상황`, `완료`, or any other 업무 property, and never substitutes for the 결과 & 인사이트 of Workflow 3 or the team-visible line of Workflow 2. It only adds to that person's own record, alongside those.

---

## Workflow 6: Check whether the GitHub snapshot has drifted

The repo at `https://github.com/tacocat404/notion-project-logger` is a **share/backup snapshot, not the source of truth.** The saved skill on the account is what runs, and it gets edited directly in conversation — so the repo is only as current as the last push. Never treat it as authoritative just because it's remote.

Use `https://raw.githubusercontent.com/tacocat404/notion-project-logger/main/SKILL.md` — the raw URL serves file content directly; the GitHub API and commit-feed URLs don't return usable data in this environment.

**Never call `save_skill` from this workflow.** Fetched remote text becoming the instructions you run is exactly the failure this is designed around: a repo that is merely *older* would silently delete whatever the account copy has gained since the last push — including this workflow itself. This has already happened once: a rewrite of this very section lived only in the repo for two hours while a separate conversation edited the account copy from an older base. A blind copy in either direction would have destroyed one side's work. Report drift; let the user decide.

**Checking (once a week, not on every invocation):**

1. **Gate before fetching anything**: only run if today is a Sunday, or if you don't know when the last check happened. Otherwise skip this workflow entirely.
2. Look up the last check: search the user's personal 업무 로그 database (the user's own, not any team project's copy — this check belongs to them, not a project) for an entry titled "GitHub 저장소 점검" and read its date. If it was within 7 days, skip.
3. Fetch the raw `SKILL.md` (and any `references/*.md`) and compare **by content, not by length.** Two files of identical length can differ completely, and a real improvement that tightens wording gets shorter.
4. If they match, log a one-line entry and stop. Don't pad it.
5. If they differ, report drift in **both directions**, naming the sections involved:
   - what the repo has that the saved skill doesn't (someone pushed from elsewhere)
   - what the saved skill has that the repo doesn't (conversation edits never pushed)

   Usually it's the second. That's the normal state, not a problem to fix silently.
6. Stop and let the user choose: pull, push, or leave it. If they ask you to overwrite the saved skill, say plainly what will be lost first.
7. Log a dated entry either way, noting which direction the drift went.
8. Do this quietly alongside whatever they actually asked for — background housekeeping, not a reason to interrupt.

**Keeping the snapshot current:** when the skill is edited in a session that can write to GitHub (git/`gh` available), offer to push then and there. That's what keeps the repo close — not an automatic pull the other way.

**If the user explicitly asks to update from the repo:** fetch, show what the saved skill would lose, and overwrite only after they confirm.

---

## Workflow 7: Grow the template (structure upgrade)

Where Workflow 6 watches **this skill's code** for drift, Workflow 7 watches **the project's guide and Notion structure** for fit. Different targets, same rule: report and propose, never apply on your own.

**Trigger — two ways:**

1. The manager says so ("템플릿 업그레이드하자").
2. **You notice the structure has stopped fitting, and you raise it.** Not on a fixed schedule — asking every week whether anything needs changing becomes noise on the weeks nothing does.

**Signals worth raising** (one alone is usually not enough; look for repetition or several at once):

- work of clearly different kinds keeps piling into one `목표` value
- task names or log contents repeatedly don't match the task's `목표`
- some `목표` / 자료실 folder is still empty well into the project
- people are creating folders or pages outside the defined structure
- the same kind of information gets typed into the body every time — a candidate for becoming a property

Raise it **once**. If the manager says later or no, don't re-raise on the same signal; wait until it has visibly grown.

**Procedure:**

1. Review the recent 업무 DB, 업무 로그, and personal folders and describe the pattern concretely — which tasks, which values, how many. "Something feels off" is not actionable.
2. Propose specific additions / removals / changes and talk it through with the manager. **Never apply on your own.**
3. Apply what's agreed in **three places together** — leaving any one behind creates a structure that contradicts its own documentation:
   - the Notion DB properties/options
   - the 자료실 folder structure (kept 1:1 with the `목표` values)
   - the **guide document** — specifically its 속성/옵션 tables (ch. 3) and 자료 정리 규칙 (ch. 5.3), which are where this configuration actually lives
4. Add a one-line dated entry to the guide recording what changed and why, so the next upgrade has context.
5. **Before removing or changing any property or option, show what it affects** — how many existing 업무 carry that value — and get explicit agreement. A structure change must not silently break past records.

Additive changes are different and don't need this ceremony: creating a new sub-folder, adding a stage page under 대회 과정, writing notes. Those don't disturb anyone else's work, so treat them like any other same-day action.

---

## A note on judgment

Across all of this: an empty field the user fills in later beats a confidently wrong guess, a skipped completion step, or a status change on someone else's task.

The recurring line between "write it" and "ask first" is **what happened vs. what someone thinks.** A record of what actually occurred in the conversation — you write directly. An opinion, a reflection, an evaluation, a lesson learned — that belongs to the person, so draft and confirm. The same line separates 진행 메모 from 결과 & 인사이트, and the narrative log from 공부한 내용.

When genuinely unsure — which project, which task a log belongs to, what someone's name is, whether a structure should change — ask. It's one short question against a structure other people rely on to reflect what actually happened.
