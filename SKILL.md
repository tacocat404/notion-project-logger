---
name: notion-project-logger
description: Acts as a team member inside any Notion project spun up from the "프로젝트 관리 템플릿" (업무 DB + 업무 로그 DB + 자료실 folders). Registers/updates 업무 items per the template's rules, runs the required 완료 wrap-up ritual, writes progress notes, logs conversations to 업무 로그, and sets up personal 자료실 folders on project kickoff. Use whenever the user adds/moves/wraps up a task, logs a conversation, or starts a new project on this template — even without saying "Notion", and even for a fresh copy with different database IDs. The user often forgets to invoke this by name: whenever a conversation touches creating/editing a Notion page, a task/업무/project tracker/work log/team project, or wrapping up work that could belong here, proactively ask whether this skill should be used rather than writing to Notion directly or letting it pass.
---

# Notion Project Logger

## Why this skill exists, and how it's meant to be reused

This template gets duplicated for each new team project — a user typically has their own personal instance plus one copy per team project. Every copy has its own 업무 and 업무 로그 data sources with fresh IDs, but the *same* property names, option values, and page-body template. So: never hardcode a data source ID as if it were universal. At the start of any task, work out which project the user means (their current page, or ask if ambiguous), then fetch that project's own 업무/업무 로그 database to confirm its schema and collection URLs before writing anything. `references/notion-schema.md` documents the shape you should expect (with placeholder IDs, not constants).

The actual operating rules — naming conventions, required fields, the completion ritual, the hold-reason requirement — come from that project's own written guide ("프로젝트 관리 템플릿 사용 가이드"), which you should fetch and read for real before doing work (see "Locating the right project" below). Following it isn't optional politeness — teammates who never wrote this skill are working from that same guide, and a task that doesn't follow the convention becomes invisible to them (e.g. missing from the board because 진행 상황 was never set).

## Locating the right project, and reading its actual guide

1. If the user is clearly working inside one project already (e.g. they mention it, or it's the only one you can find near the conversation context), use that.
2. If unclear or multiple candidates exist, search for "프로젝트 관리 템플릿" or the project's name and ask which one before writing.
3. Fetch the project's 업무 database to get its exact data source URL and schema — don't reuse a schema from a different project's fetch result, even if it looks identical.
4. **Required, not optional: find and read that project's own "프로젝트 관리 템플릿 사용 가이드" page** (it lives under 자료실 > 문서 폴더(전체가 읽어야하는 것)) before doing any write action in Workflows 1-4. Treat it as the actual operating instructions for this run — not `references/template-rules.md`. Different copies of the template can drift from the original as teams adapt it, and the live page is always the current truth for that specific project. Do not skip this fetch because you already "know" the rules from a previous project or from this file — a project's guide may have been edited. Only fall back to `references/template-rules.md` if the project genuinely has no guide page (missing or broken), and say so when you do.

## Workflow 1: Register a 업무

`이름`, `진행 상황`, and `날짜` are hard requirements per the guide — do not create the 업무 page until all three are filled. This isn't a style preference: a task missing `진행 상황` or `날짜` is genuinely invisible on the board or calendar respectively, so a "created" task without them is silently broken, not just untidy.

1. Task name in verb form ("로그인 API 구현", not "로그인 API") — this is a house convention, not a suggestion. If the user gives a vague or noun-form name (e.g. "테스트 프로그램"), ask them to firm it up into a specific, verb-form name rather than guessing one or creating the task under a name that won't mean anything later.
2. `진행 상황`: default to `시작 전👀` if the user doesn't say otherwise — this one you may default rather than ask about, since the guide names it the default for new tasks.
3. `날짜`: never default this one silently. If the user hasn't given a date, ask for it and wait for an answer before creating the page.
4. Map the task's nature onto `목표` (학습/기획/조사/구현/수정/테스트 및 검증) — this also determines which 자료실 folder its output eventually goes in, so pick the closest real fit rather than skipping it. Propose your best guess and have the user confirm it, same as with any other field you're inferring.
5. Ask about `중요도` and `담당자` too, don't silently default them. It's fine to propose a default while asking ("담당자는 너로 할게, 맞지?" / "중요도는 특별히 급한 거 없으면 ⭐⭐⭐로 할까?") so the user can just confirm instead of typing it out — but a silent default the user never saw is a value they can't correct.
6. If the task looks like it'll take more than ~2 weeks, suggest splitting it into sub-items linked via `상위 업무` rather than registering one giant task — this is what the guide asks for, and a wall of one task is as unhelpful as no task.

## Workflow 2: Update an in-progress 업무

Three different kinds of "update" map to three different places — don't conflate them:

- **A quick fact/decision/blocker about one specific task** → append a dated line to that task's own "진행 메모" section in its page body. This is a private-ish running journal for that task, not a team broadcast. Same rule as the completion ritual: if the note is really the user's own take/decision rather than a plain fact from the conversation (e.g. "I think we should switch approaches because..."), ask them to confirm the wording before writing it in as their voice.
- **A status change** (시작 전→기획 중→진행 중→완료, or →보류) → change the `진행 상황` property. Recognize the cues yourself rather than waiting to be told to "change the status": if the user says they're starting/designing the approach, that's `기획 중📝`; once they're actually doing the work, move it to `진행 중🏃‍♀️`; if they say they're stuck or waiting on someone/something external, move it to `보류⛔` and write the reason; if they say it's done, that's the trigger for the full Workflow 3 completion ritual below, not just flipping this one property. When genuinely ambiguous which stage a comment implies, ask — but plenty of the time the conversation itself makes it obvious, and that's precisely the "understanding the structure" this skill is for. Moving to `보류⛔` requires writing the reason in the body — an unexplained hold is something the guide explicitly calls out as blocking teammates from helping.
- **Something worth other teammates seeing** (a shared update, a question, an answer, a summary of a work session/conversation) → create an entry in the 업무 로그 database with `관련 업무` pointing at the task(s) it concerns. If it's genuinely unclear which task a conversation relates to, ask rather than guessing — an entry linked to the wrong task is worse than one left unlinked.
- **Don't change the `진행 상황` of a task someone else owns.** Leave a comment instead (per the guide's own rule) — silently moving someone else's card is the kind of thing that erodes trust in an automated teammate fast.

## Workflow 3: Complete a 업무 (the wrap-up ritual)

The guide marks this "제일 중요" (most important) — skipping a step means the work becomes invisible history later. Do all four, in order:

1. **Ask the user for the actual 결과 & 인사이트** — what came out of it, what they'd do differently next time — rather than writing it yourself. This section is the person's own reflection and judgment; a plausible-sounding paragraph you generated isn't the same thing, even if it reads fine. You can draft a starting point from what happened in the conversation and offer it for them to confirm or rewrite, but don't post it to Notion as their insight without them signing off on it.
2. If there's an actual output/document, create it inside the matching *shared* 자료실 folder (the 목표-matched one under 문서 폴더(전체가 읽어야하는 것) — 학습/기획/조사/구현/수정/테스트 및 검증), named `YYYY-MM-DD 주제` (e.g. "2026-08-04 온보딩 체크리스트 초안"), then link it from the task body (an @-mention-style reference). This is official, team-visible output tied to a registered 업무 — it does not go in anyone's personal folder. Personal folders are for what an individual wants to keep for themselves (reflections, half-formed notes, things not yet ready to be "the record") — never file an official task deliverable there just because one person did the work.
3. Move `진행 상황` to `완료👍`.
4. Check the `완료` checkbox.

A task marked done with an empty write-up is exactly the kind of record the whole template exists to prevent, so don't treat step 1–2 as optional even if the user only asked you to "check it off."

## Workflow 4: Kick off a new team project

Trigger: the user is duplicating this template for a new project and wants it set up.

The 자료실 pattern in every existing copy is: one shared "문서 폴더(전체가 읽어야하는 것)" with the six 목표-matched sub-folders, plus one plain page per team member titled with just their real name (no prefix/suffix — e.g. 홍길동, 김영희).

1. Ask, in one message: 프로젝트 이름, 참여자들의 실명(개인 폴더용), 지금 등록하고 싶은 초기 업무가 있는지(있으면 이름/담당자/목표), 중요한 마감일. Don't invent any of these — an empty skeleton the user fills in beats one seeded with guesses.
2. Create one personal folder page per name given, following the naming convention above exactly (just the name, empty inside) — don't pre-fill structure into it. Personal folders aren't standardized like 업무/자료실 documents are; different people keep different things there (some a running diary, some just scratch notes, some nothing at all). Setting up the page is fine on your own judgment; writing actual content into someone's personal folder is not — check what's already in there, or ask that person, before adding anything beyond the bare page.
3. Register any initial 업무 the user provided, via Workflow 1.
4. Confirm back with what was created so a typo'd name or wrong task gets caught immediately.

## A note on judgment

Across all of this: an empty or unset field the user can fill in later beats a confidently wrong guess, a skipped completion step, or a status change on someone else's task. When genuinely unsure — which project, which task a log belongs to, what someone's name is, or what belongs in someone's personal folder — ask; it's one short question against a structure other people rely on to actually reflect what happened.
