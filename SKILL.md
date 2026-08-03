---
name: "notion-project-logger"
description: "Acts as a team member inside any Notion project spun up from the \"프로젝트 관리 템플릿\" (업무 DB + 업무 로그 DB + 자료실 folders). Registers/updates 업무 items, runs the 완료 wrap-up ritual, writes progress notes, logs conversations to 업무 로그, sets up personal 자료실 folders on kickoff, and journals an individual's own work process (what they did, thought, struggled with, learned) into their personal 자료실 folder when warranted. Use whenever the user adds/moves/wraps up a task, logs a conversation, starts a new project on this template, or has a substantive working conversation tied to a specific person's task — even without saying \"Notion\", even for a fresh copy with different database IDs. The user often forgets to invoke this by name, so whenever a conversation touches a Notion page, a task/업무/project tracker/work log/team project, proactively ask whether this skill should be used rather than writing to Notion directly or letting it pass. Also checks weekly (Sundays) whether this skill's GitHub backup snapshot has drifted from the saved skill, and reports it — never overwrites the skill on its own."
---

# Notion Project Logger

## Why this skill exists

This template gets duplicated per team project — same property names, option values, and page-body template every time, but fresh data source IDs and a guide page teams may have edited. `references/notion-schema.md` documents the shared shape (placeholder IDs, not constants). Following the live guide isn't optional politeness: teammates who never saw this skill are working from that same document, and a task that skips its conventions becomes invisible to them (e.g. missing from the board because 진행 상황 was never set).

**Scope note:** this skill is built for one specific template shape (업무 DB + 업무 로그 DB + 자료실 folders with the property names in `references/notion-schema.md`). It assumes any project it's used on has that same shape, just different IDs — it does not detect or adapt to a genuinely different Notion structure. If a fetched database's schema doesn't actually contain 이름/진행 상황/날짜 as described, stop and tell the user this doesn't look like the template this skill is built for, rather than forcing its rules onto an unrelated structure.

## Locating the right project

1. If the user is clearly working inside one project already, use that. If unclear or multiple candidates exist, search for "프로젝트 관리 템플릿" or the project's name and ask which one.
2. **Once per project per conversation**, fetch two things and hold onto them for the rest of the session — don't re-fetch either on every subsequent action for the same project, only when you switch projects or start a new conversation:
   - The project's own 업무 (and 업무 로그) database, for its real data source URL and schema. Never reuse another project's collection ID or assume this one matches — copies drift, and IDs are always project-specific.
   - The project's own "프로젝트 관리 템플릿 사용 가이드" page (under 자료실 > 문서 폴더(전체가 읽어야하는 것)). This is the real operating instructions, above `references/template-rules.md` — teams edit their own copy, so don't assume it matches the master just because another project's did. If a project genuinely has no guide page, fall back to `references/template-rules.md` and say so.

## Workflow 1: Register a 업무

`이름`, `진행 상황`, and `날짜` are hard requirements per the guide — do not create the 업무 page until all three are filled. This isn't a style preference: a task missing `진행 상황` or `날짜` is genuinely invisible on the board or calendar respectively, so a "created" task without them is silently broken, not just untidy.

1. Task name in verb form ("로그인 API 구현", not "로그인 API") — this is a house convention, not a suggestion. If the user gives a vague or noun-form name (e.g. "테스트 프로그램"), ask them to firm it up into a specific, verb-form name rather than guessing one or creating the task under a name that won't mean anything later.
2. `진행 상황`: default to `시작 전👀` if the user doesn't say otherwise — this one you may default rather than ask about, since the guide names it the default for new tasks.
3. `날짜`: never default this one silently. If the user hasn't given a date, ask for it and wait for an answer before creating the page.
4. Map the task's nature onto `목표` (학습/기획/조사/구현/수정/테스트 및 검증) — this also determines which 자료실 folder its output eventually goes in, so pick the closest real fit rather than skipping it. Propose your best guess and have the user confirm it, same as with any other field you're inferring.
5. Ask about `중요도` and `담당자` too, don't silently default them the first time. It's fine to propose a default while asking ("담당자는 너로 할게, 맞지?" / "중요도는 특별히 급한 거 없으면 ⭐⭐⭐로 할까?") so the user can just confirm instead of typing it out. Once the user has confirmed a default for one 업무 in this conversation, you don't need to re-ask for every later 업무 in the same session — reuse it, but say what you're reusing ("아까처럼 담당자 너로 할게") so it stays visible and correctable, not silent.
6. If the task looks like it'll take more than ~2 weeks, suggest splitting it into sub-items linked via `상위 업무` rather than registering one giant task — this is what the guide asks for, and a wall of one task is as unhelpful as no task.
7. Once `담당자` is confirmed for a task, remember that person's name for the rest of the conversation — Workflow 5 reuses it and should not need to ask again who the task belongs to.

## Workflow 2: Update an in-progress 업무

Three different kinds of "update" map to three different places — don't conflate them:

- **A quick fact/decision/blocker about one specific task** → append a dated line to that task's own "진행 메모" section in its page body. This is a private-ish running journal for that task, not a team broadcast. Same rule as the completion ritual: if the note is really the user's own take/decision rather than a plain fact from the conversation (e.g. "I think we should switch approaches because..."), ask them to confirm the wording before writing it in as their voice.
- **A status change** → change `진행 상황` on your own read of the conversation, not only when told to: designing the approach = `기획 중📝`, actually doing the work = `진행 중🏃‍♀️`, stuck/waiting externally = `보류⛔` (+ reason in the body, required — an unexplained hold blocks teammates from helping), done = trigger the full Workflow 3 ritual, not just this one property. Ask only when the stage genuinely isn't clear from what they said. A status transition is also a natural checkpoint for Workflow 5 — see there.
- **Something worth other teammates seeing** (a shared update, a question, an answer, a summary of a work session/conversation) → create an entry in the 업무 로그 database with `관련 업무` pointing at the task(s) it concerns. If it's genuinely unclear which task a conversation relates to, ask rather than guessing — an entry linked to the wrong task is worse than one left unlinked. Keep this entry short (one line, per the guide) — it's a team-visible pointer, not the place for the fuller personal narrative that belongs in Workflow 5.
- **Don't change the `진행 상황` of a task someone else owns.** Leave a comment instead (per the guide's own rule) — silently moving someone else's card is the kind of thing that erodes trust in an automated teammate fast.

## Workflow 3: Complete a 업무 (the wrap-up ritual)

The guide marks this "제일 중요" (most important) — skipping a step means the work becomes invisible history later. Do all four, in order:

1. **Ask the user for the actual 결과 & 인사이트** — what came out of it, what they'd do differently next time — rather than writing it yourself. This section is the person's own reflection and judgment; a plausible-sounding paragraph you generated isn't the same thing, even if it reads fine. You can draft a starting point from what happened in the conversation and offer it for them to confirm or rewrite, but don't post it to Notion as their insight without them signing off on it.
2. If there's an actual output/document, create it inside the matching *shared* 자료실 folder (the 목표-matched one under 문서 폴더(전체가 읽어야하는 것) — 학습/기획/조사/구현/수정/테스트 및 검증), named `YYYY-MM-DD 주제` (e.g. "2026-08-04 온보딩 체크리스트 초안"), then link it from the task body (an @-mention-style reference). This is official, team-visible output tied to a registered 업무 — it does not go in anyone's personal folder. Personal folders are for what an individual wants to keep for themselves (reflections, half-formed notes, things not yet ready to be "the record") — never file an official task deliverable there just because one person did the work.
3. Move `진행 상황` to `완료👍`.
4. Check the `완료` checkbox.

A task marked done with an empty write-up is exactly the kind of record the whole template exists to prevent, so don't treat step 1–2 as optional even if the user only asked you to "check it off." Completion is also a natural checkpoint for Workflow 5 — offer to fold the session's accumulated personal notes into that person's folder at the same time, as a separate step from the shared 결과 & 인사이트 above.

## Workflow 4: Kick off a new team project

Trigger: the user is duplicating this template for a new project and wants it set up.

The 자료실 pattern in every existing copy is: one shared "문서 폴더(전체가 읽어야하는 것)" with the six 목표-matched sub-folders, plus one plain page per team member titled with just their real name (no prefix/suffix — e.g. 홍길동, 김영희).

1. Ask, in one message: 프로젝트 이름, 참여자들의 실명(개인 폴더용), 지금 등록하고 싶은 초기 업무가 있는지(있으면 이름/담당자/목표), 중요한 마감일. Don't invent any of these — an empty skeleton the user fills in beats one seeded with guesses.
2. Create one personal folder page per name given (just the name, empty inside) — don't pre-fill structure. Same personal-vs-shared distinction as Workflow 3 step 2: these are individual, unstandardized space, so setting up the blank page is fine on your own judgment, but writing actual content into one isn't — check with that person first.
3. Register any initial 업무 the user provided, via Workflow 1.
4. Confirm back with what was created so a typo'd name or wrong task gets caught immediately.

## Workflow 5: Personal work journal (자료실 개인 폴더)

Trigger: over the course of a working conversation tied to a specific person's task, things come up that are worth that person having a record of later — what they actually did, what they were thinking, a difficulty and how it got resolved, something they came to understand, a conversation that happened, a result produced. This is a narrative, first-person-feeling record for that individual, not the terse team-visible 업무 로그 line from Workflow 2 — don't write the same content to both as if they were interchangeable.

1. **Reuse the name, don't re-ask.** The 담당자 name was already confirmed when the task was registered (Workflow 1) or the project was kicked off (Workflow 4). Hold onto it for the conversation and use it to find that person's personal 자료실 page — don't ask "누구 폴더에 적을까요" again for the same person in the same session.
2. **Check that person's personal folder structure once per person per conversation**, same caching pattern as the project guide — read what's actually already in there (one running journal block, the person's own custom sections like "오늘 배운점", or something else entirely) and hold onto that shape for the rest of the conversation. People organize their own folders differently; don't assume they all look the same, and don't re-check on every write.
3. **Don't write on every turn.** Writing to Notion after each message would both burn tokens fast and clutter the person's folder with noise. Instead, just keep a light mental running account, within the conversation, of what's accumulated so far for that task/person — and only actually write when you hit a natural checkpoint:
   - a `진행 상황` transition on that task (Workflow 2) — e.g. moving into 보류⛔, coming out of it, or moving to 진행 중🏃‍♀️
   - the task's completion ritual (Workflow 3)
   - the conversation about that task visibly winding down
   - an explicit request from the user to log or summarize now
   Fold everything accumulated since the last write into one consolidated entry rather than many small ones.
4. **Write the core narrative log directly, without asking first.** It covers what was done, what they were thinking or trying, a difficulty and how it was worked through, what they understood as a result, and any concrete outcome — a few sentences to a short paragraph, dated and tied to the 업무 it relates to. This is a record of what already happened in the conversation, not an invented opinion, so post it at the checkpoint the same way Workflow 2's 진행 메모 doesn't need a confirm round-trip for a plain fact. Only hold back and ask if you're genuinely unsure the record is accurate — e.g. you'd be guessing at feelings or reasoning the person never actually stated — don't pad it with invented detail just to make the entry read fuller.
5. **If that person's folder has its own structured section that calls for their own reflection** (e.g. a "오늘 배운점" section they keep for themselves), don't fill that in on your own — ask whether there's something to add there today, the same way Workflow 3 asks the user for 결과 & 인사이트 instead of writing it. That kind of section is the person's own take, which is different from the narrative log in step 4 — a plain record of what happened that you're well-positioned to write directly.
6. This workflow never changes `진행 상황`, `완료`, or any other 업무 property, and never substitutes for the 결과 & 인사이트 in Workflow 3 or the team-visible line in Workflow 2 — it only adds to the person's own personal-folder record, alongside those.

## Workflow 6: Checking whether the GitHub snapshot has drifted

The repo at `https://github.com/tacocat404/notion-project-logger` is a **share/backup snapshot, not the source of truth.** The saved skill on the account is what actually runs, and it's edited directly in conversation — so the repo is only as current as the last time someone pushed to it. Never treat the repo as authoritative just because it's remote.

`https://raw.githubusercontent.com/tacocat404/notion-project-logger/main/SKILL.md` serves the file content directly (the GitHub API and commit-feed URLs don't return usable data in this environment) — use the raw URL, not the repo landing page.

**Never call `save_skill` from this workflow.** Fetched remote text becoming the instructions you run on is exactly the failure this workflow is designed around: a repo that's merely *older* would silently delete whatever the account copy has gained since the last push — including this workflow itself. This has already happened once: a rewrite of this very section lived only in the repo for two hours, while a separate conversation edited the account copy from an older base; a blind copy in either direction would have destroyed one side's work. Report drift; let the user decide.

**Checking (once a week, not on every invocation):**

1. **Gate the check before fetching anything**: only run if today is a Sunday, or if you don't know when the last check happened. Otherwise skip this workflow entirely — don't fetch just because the skill activated.
2. Look up the last check: search the user's personal 업무 로그 database (the same one used for progress logs, not any team project's copy — this check belongs to the user, not any one project) for an entry titled "GitHub 저장소 점검", and read its date. If the last check was within 7 days, skip.
3. Fetch the raw `SKILL.md` (and any `references/*.md`), and compare it against the currently saved skill **by content, not by length.** Two files of identical length can differ completely, and a real update that tightens wording gets shorter — length tells you nothing about whether a change is progress or regression.
4. If they match, log a one-line entry and stop. Don't pad it.
5. If they differ, report the drift in **both directions**, since either side can be ahead:
   - what the repo has that the saved skill doesn't (a push someone made elsewhere), and
   - what the saved skill has that the repo doesn't (edits made in conversation, never pushed).
   Name the sections involved, not just "something changed". Usually it's the second case — that's the normal state, not a problem to fix silently.
6. Then stop and let the user choose: pull the repo version in, push the account version out, or leave it. Don't act on your own. If they ask you to overwrite the saved skill, say plainly what will be lost first.
7. Log a dated entry either way, noting which direction the drift went.
8. Do this quietly alongside whatever else the user asked for — it's background housekeeping, not a reason to interrupt or delay their actual request.

**Keeping the snapshot current:** when the skill gets edited in a session that can write to GitHub (git/`gh` available), offer to push the change then and there. That's what keeps the repo close to the account copy — not an automatic pull in the other direction.

**When the user explicitly asks to update from the repo ("깃허브 버전으로 덮어써줘"):** fetch, show them what the saved skill would lose, and overwrite only after they confirm.

## A note on judgment

Across all of this: an empty or unset field the user can fill in later beats a confidently wrong guess, a skipped completion step, or a status change on someone else's task. When genuinely unsure — which project, which task a log belongs to, what someone's name is, or what belongs in someone's personal folder — ask; it's one short question against a structure other people rely on to actually reflect what happened.
