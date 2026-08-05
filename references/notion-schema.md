# Data shape reference

This describes the *shape* every copy of the template has. **All IDs below are placeholders.** Collection URLs differ per copy — always fetch the current project's own database before writing, and never reuse an ID from another project.

Values are split into two kinds, and the split matters:

- **Fixed part** — the skeleton every copy shares. Safe to rely on.
- **Variable part** — values that legitimately differ per project and change over time. **Never rely on what's written here for these; read them from the project's 「프로젝트 관리 템플릿 사용 가이드」 and from the live data source.** The lists shown are what a fresh copy starts with, not what any given project currently has.

This is the same principle as not hardcoding IDs, applied one level up: a copy that has grown into its project will have different options than the master, and that is the intended behavior, not drift to correct.

---

## 업무 data source

### Fixed part — present in every copy

| property | type | notes |
|---|---|---|
| 이름 | title | required, verb form |
| 진행 상황 | select | required — options are variable, see below |
| 날짜 | date | required. `date:날짜:start` / `date:날짜:end` / `date:날짜:is_datetime` |
| 완료 | checkbox | |
| 목표 | select | options are variable, see below |
| 담당자 | person | array of user ids |
| 상위 업무 | relation | -> same data source |
| 하위 할 일 | relation | -> same data source (auto-filled from the child's 상위 업무) |
| 업무 로그 | relation | -> that project's 업무 로그 data source |

### Variable part — read from the project, don't assume

| property | what varies |
|---|---|
| 진행 상황 | the stage list. A fresh copy starts with 시작 전👀 / 기획 중📝 / 진행 중🏃‍♀️ / 보류⛔ — **no completion stage**, because done is the `완료` checkbox alone (one way to say done, so the two can't disagree) |
| 목표 | the option list, **and therefore the 자료실 folder set** (they stay 1:1). A fresh copy starts with 📖ㅣ학습 / 📋ㅣ기획 / 🔎ㅣ조사 / 💻ㅣ구현 / 💀ㅣ수정 / 🥵ㅣ테스트 및 검증 |
| 중요도 | a fresh copy starts with ⭐⭐⭐⭐⭐ / ⭐⭐⭐ / ⭐ |
| project-specific properties | a project may add its own (e.g. a 회차-style property for repeated stages). Whatever exists is documented in that project's guide |

---

## 업무 로그 data source

| property | type | notes |
|---|---|---|
| 내용 | title | required, one line |
| 작성자 | person | |
| 날짜 | date | |
| 관련 업무 | relation | -> that project's 업무 data source |

Written by people, not by the skill (SKILL.md Workflow 2).

---

## 업무 page body template

Every 업무 page starts from the data source's default template, which has four sections. Each contains a 👉 callout holding instruction text.

```
### 1. 목표              — one sentence: what this task is for
### 2. 진행 메모          — dated running journal; append, don't overwrite
### 3. 결과 & 인사이트    — filled at completion; the person's own reflection
---
### 참고 자료 및 링크     — @-mention links to docs in 문서 폴더
```

**Convention:** content is appended *inside* the existing callout, keeping the instruction text. Don't delete the callout or write outside it.

---

## 자료실 structure

```
문서 폴더(전체가 읽어야하는 것)
├── 프로젝트 관리 템플릿 사용 가이드   ← the skill's live configuration
├── (other reading material — 기록의 필요성, 글, … : not configuration)
└── one sub-folder per 목표 option     ← variable, kept 1:1 with the option list

<name>/                                ← one per member; name is whatever they chose
├── 공부한 내용        (the person writes)
├── 대회 과정          (skill writes: stage-by-stage digest)
└── 업무 및 대화 로그   (skill writes: time-ordered original)
```

Personal folder names may be real names or nicknames — use whatever the person chose, don't normalize.

---

## Views

A fresh copy has table / board (grouped by 진행 상황) / calendar (by 날짜) views, plus a 이번 주 할 일 view grouped by 중요도.

**All four hide completed tasks** (`FILTER "완료" = false`), and the project page carries one extra section at the bottom showing only completed ones (`FILTER "완료" = true`). Checking the box moves a task from the working screens to that section.

Filter DSL note: write `= true` / `= false` for checkboxes. Passing `"__YES__"` silently produces the *opposite* filter (`checkbox_is_not true`) — that string form belongs to SQL queries, not view configuration.

---

## Working with the data

Find in-progress 업무 (substitute the real collection URL and the project's actual stage value):
```sql
SELECT url, "이름" FROM "collection://<업무 id>" WHERE "진행 상황" = '<the project''s in-progress stage>'
```

Keyword search on name:
```sql
SELECT url, "이름" FROM "collection://<업무 id>" WHERE "이름" LIKE '%키워드%'
```

Creating pages: use notion-create-pages with parent `{"type": "data_source_id", "data_source_id": "<id from that project's collection:// url>"}` for 업무/업무 로그 rows, or `{"type": "page_id", "page_id": "<자료실 page id>"}` for folder pages.
