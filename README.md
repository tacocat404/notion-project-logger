# notion-project-logger

A Claude skill that acts like a team member inside a Notion project built from a specific "프로젝트 관리 템플릿" (project management template) — one 업무(task) database, one 업무 로그(work log) database, and a 자료실(resource room) folder structure.

## What it does

- **Registers tasks** (업무) with the required properties the template's official guide demands — verb-form names, 진행 상황(status), 날짜(date) — and asks rather than silently defaulting or guessing when a value is missing or ambiguous.
- **Tracks progress**, distinguishing three different kinds of "update" the template treats differently: a private task journal entry (진행 메모), a status change (진행 상황), and a team-visible log entry (업무 로그) linked back to the task it concerns.
- **Runs the task completion ritual** the guide calls "제일 중요" (most important): write the result & insight, file any output doc in the correct shared folder, move status to done, check the checkbox — all four steps, in order.
- **Sets up new projects**, creating per-teammate personal folders and seeding initial tasks when a new copy of the template is kicked off.

## Why it's built this way

The template gets copied for every new project, and each copy gets its own database IDs. So the skill never hardcodes a data source ID — it looks up the current project's actual database and, critically, **fetches and reads that project's own guide page every time** rather than relying on a cached summary, since teams can and do adapt the guide as they go.

It also draws a hard line around anything that's a personal judgment call: results & insights, task retrospectives, and the contents of someone's personal folder are never fabricated by the skill — it drafts a starting point at most, and always gets the person's own sign-off before writing it into Notion as their voice.

## Files

- `SKILL.md` — the skill definition Claude loads (name, trigger description, and the four workflows above).
- `references/notion-schema.md` — the property/type shape shared by every copy of the template, with placeholder IDs (each project's real ones are looked up at runtime).
- `references/template-rules.md` — a distilled fallback summary of the official guide's rules, used only if a project's live guide page can't be found.

## Origin

Built and tested iteratively in a real Notion workspace (registering, updating, holding, resuming, and completing an actual task end-to-end) as a learning exercise in how Claude skills are structured and refined. Written up as part of that process — see the accompanying blog post for the design decisions and what changed between iterations.

## Install

This is a Cowork/Claude skill package (`.skill` = a zip of this folder). To use it:
1. Open the `.skill` file in Cowork and click "Save skill", or
2. Copy this folder into your skills directory if you're running Claude Code / the Agent SDK directly.
