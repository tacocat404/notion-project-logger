---
name: notion-project-logger
description: Acts as a team member inside any Notion project spun up from the "프로젝트 관리 템플릿" (업무 DB + 업무 로그 DB + 자료실 folders). Registers/updates 업무 items per the template's rules, runs the required 완료 wrap-up ritual, writes progress notes, logs conversations to 업무 로그, and sets up personal 자료실 folders on project kickoff. Use whenever the user adds/moves/wraps up a task, logs a conversation, or starts a new project on this template — even without saying "Notion", and even for a fresh copy with different database IDs. The user often forgets to invoke this by name, so whenever a conversation touches creating/editing a Notion page, a task/업무/project tracker/work log/team project, or wrapping up work that could belong here, proactively ask whether this skill should be used rather than writing to Notion directly or letting it pass. Also checks weekly (Sundays) whether this skill's own GitHub source has a new commit and logs the result.
---

# Notion Project Logger

## Why this skill exists

This template gets duplicated per team project — same property names, option values, and page-body template every time, but fresh data source IDs and a guide page teams may have edited. `references/notion-schema.md` documents the shared shape (placeholder IDs, not constants). Following the live guide isn't optional politeness: teammates who never saw this skill are working from that same document, and a task that skips its conventions becomes invisible to them (e.g. missing from the board because 진행 상황 was never set).

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

## Workflow 2: Update an in-progress 업무

Three different kinds of "update" map to three different places — don't conflate them:

- **A quick fact/decision/blocker about one specific task** → append a dated line to that task's own "진행 메모" section in its page body. This is a private-ish running journal for that task, not a team broadcast. Same rule as the completion ritual: if the note is really the user's own take/decision rather than a plain fact from the conversation (e.g. "I think we should switch approaches because..."), ask them to confirm the wording before writing it in as their voice.
- **A status change** → change `진행 상황` on your own read of the conversation, not only when told to: designing the approach = `기획 중📝`, actually doing the work = `진행 중🏃‍♀️`, stuck/waiting externally = `보류⛔` (+ reason in the body, required — an unexplained hold blocks teammates from helping), done = trigger the full Workflow 3 ritual, not just this one property. Ask only when the stage genuinely isn't clear from what they said.
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
2. Create one personal folder page per name given (just the name, empty inside) — don't pre-fill structure. Same personal-vs-shared distinction as Workflow 3 step 2: these are individual, unstandardized space, so setting up the blank page is fine on your own judgment, but writing actual content into one isn't — check with that person first.
3. Register any initial 업무 the user provided, via Workflow 1.
4. Confirm back with what was created so a typo'd name or wrong task gets caught immediately.

## Workflow 5: Checking whether this skill itself has an update

This skill's source lives at `https://github.com/tacocat404/notion-project-logger` (public repo). `https://raw.githubusercontent.com/tacocat404/notion-project-logger/main/SKILL.md` serves the actual file content directly (unlike the GitHub API or commit-feed URLs, which don't return usable data in this environment) — use the raw URL, not the repo landing page, since it gives real content to compare instead of just a commit count.

**Checking (once a week, not on every invocation):**

1. **Gate the check first, before fetching anything**: only run this if today (per current date) is a Sunday, or if you don't yet know when the last check happened. Otherwise skip this workflow entirely — don't fetch anything just because the skill activated.
2. Find the last known state: search the user's personal 업무 로그 database (the same one used for progress logs, not any team project's copy — this check belongs to the user, not any one project) for an entry titled "GitHub 저장소 점검". Read its most recent recorded content length (character count of the fetched SKILL.md — a cheap stand-in for a hash) and check-date from the entry body.
3. If the last check was already this week (within 7 days), skip — no need to re-fetch.
4. Otherwise, fetch the raw SKILL.md URL above (and any `references/*.md` the repo lists) and compare its content length to the last recorded one. Different length (or no prior record) = something changed since last check.
5. **If it differs, auto-update the saved skill right away** — call `save_skill` with `overwrite: true`, using the freshly fetched raw content as the new body and its frontmatter `description` as the new description. Do this automatically, without asking first; the user has said they want this hands-off. Then write a dated entry to the "GitHub 저장소 점검" log noting the new content length and that the saved skill was auto-updated. Tell the user in the conversation, briefly, that this happened (one line — not a diff, just "노션 로거 스킬이 깃허브 변경사항으로 자동 업데이트됐어").
6. One real risk with blind auto-update: the repo could be *behind* the currently-saved skill (e.g. if the account copy picked up local fixes the repo hasn't seen yet) — in that case auto-updating would regress it. This has already happened once with this repo, so if the fetched content is suspiciously shorter (say, more than ~20% fewer characters) than what's currently saved, skip the auto-update for that run, say why, and ask before overwriting — that specific case is the one situation worth pausing for. Anything else that merely differs, update automatically per step 5.
7. If unchanged from last check, just log a short one-line entry — don't pad it, and don't touch the saved skill.
8. Do all of this quietly alongside whatever else the user asked for in that message — it's a background housekeeping check, not something that should interrupt or delay their actual request.

**Updating, when the user explicitly asks for it mid-conversation (e.g. "업데이트해줘"):** same as step 5 above — fetch the raw content, apply the same shrink-guard from step 6, and if it passes, call `save_skill` with `overwrite: true` directly without asking for confirmation first.

## A note on judgment

Across all of this: an empty or unset field the user can fill in later beats a confidently wrong guess, a skipped completion step, or a status change on someone else's task. When genuinely unsure — which project, which task a log belongs to, what someone's name is, or what belongs in someone's personal folder — ask; it's one short question against a structure other people rely on to actually reflect what happened.
