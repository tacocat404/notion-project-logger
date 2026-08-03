# Operating rules (distilled from "프로젝트 관리 템플릿 사용 가이드")

Source of truth: the project's own "문서 폴더 > 프로젝트 관리 템플릿 사용 가이드" page. If a project's guide differs from this summary, defer to the project's actual guide page — this file is a fast reference, not an override.

## Core concept
One database (업무) holds everything; different screens (등록/보드/이번 주/달력/자료실) are just views of it. Big and small work aren't split into separate systems — a big task gets broken into smaller 업무 linked via 상위 업무/하위 할 일 instead.

## 업무 property rules
| property | required? | rule |
|---|---|---|
| 이름 | required | verb form ("로그인 API 구현", not "로그인 API") |
| 진행 상황 | required | missing = invisible on the board |
| 날짜 | required | missing = invisible on the calendar |
| 목표 | recommended | determines which 자료실 folder the output goes in |
| 중요도 | recommended | drives grouping on "이번 주 할 일" |
| 담당자 | recommended | one person |
| 상위 업무 | optional | use when splitting a >2주 task |
| 하위 할 일 | automatic | don't set directly, fills from 상위 업무 on the child |
| 완료 | optional | checkbox, part of the completion ritual |

진행 상황 stages (in order): 시작 전👀 (default for new tasks) → 기획 중📝 → 진행 중🏃‍♀️ → 완료👍, with 보류⛔ as a branch off 진행 중 that returns to 진행 중 once unblocked. Moving to 보류⛔ requires a one-line reason in the body — an unexplained hold blocks teammates from helping.

목표 ↔ 자료실 folder mapping (also the guide's criteria for choosing one):
- 📖ㅣ학습 (learning something new) → 학습 문서
- 📋ㅣ기획 (defining what to build) → 기획 문서
- 🔎ㅣ조사 (research / case analysis) → 조사 문서
- 💻ㅣ구현 (actually building it) → 구현 문서
- 💀ㅣ수정 (patching/improving existing output) → 수정 내역 문서
- 🥵ㅣ테스트 및 검증 (verifying results) → 테스트 및 검증 문서

목표 rarely changes once set (it's the task's nature); 진행 상황 changes constantly (it's the task's state). Don't conflate updating one with updating the other.

중요도: ⭐⭐⭐⭐⭐ = must finish today / delay affects downstream schedule, ⭐⭐⭐ = must finish this week, ⭐ = whenever there's slack.

## 업무 page body template
Every 업무 page's content follows this shape (from the project's default page template):
1. **목표** — one sentence: what this task is trying to achieve.
2. **진행 메모** — a running, dated journal of things learned/blocked/decided while working on it. Append, don't overwrite.
3. **결과 & 인사이트** — filled in at completion: what came out of it, what to do differently next time.
4. **참고 자료 및 링크** — `@`-mention links to related docs in 문서 폴더.

## 업무 로그 database
Separate from the in-page 진행 메모. This is the team-visible channel for progress sharing and Q&A — "최신 글이 위로 옵니다" (most recent first). Always set 관련 업무 when the content is actually about a specific task; an unlinked log entry is much harder to find later.

## The completion ritual (guide calls this "제일 중요" — most important)
1. Write 결과 & 인사이트 in the task body.
2. If there's an output artifact, create it in the matching 자료실 folder titled `YYYY-MM-DD 주제` (e.g. "2026-08-04 온보딩 체크리스트 초안"), then link it from the task body via `@`-mention. A doc that isn't linked back becomes nearly unfindable later — creating it isn't enough on its own.
3. Move 진행 상황 to 완료👍.
4. Check 완료.
Skipping steps 1-2 because "it's just a checkbox" defeats the purpose of the whole template.

## Other operating rules
- Splitting rule: if a task looks like >2 weeks of work, split it into sub-업무 linked via 상위 업무 rather than tracking it as one giant item.
- Ownership rule: don't change the 진행 상황 of a task someone else owns — leave a comment instead.
- 자료실 filing: new docs go in the *shared* folder matching their 업무's 목표 (never a personal folder), named `YYYY-MM-DD 주제`, and must be linked back from the originating 업무's body — filing without linking defeats the point. Personal folders (one per team member, named with their real name) are for an individual's own notes/reflections, not for official task deliverables — those two things are not interchangeable even when only one person did the work.
