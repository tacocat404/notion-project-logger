# Data shape reference

This describes the *shape* every copy of the template has. Collection URLs differ per project copy — always fetch the current project's own database to get the real collection:// URL before writing. Don't reuse an ID from a different project.

## 업무 data source — schema (property names/types are identical across every copy)

| property | type | notes |
|---|---|---|
| 이름 | title | required, verb form |
| 진행 상황 | select | 시작 전👀 / 기획 중📝 / 진행 중🏃‍♀️ / 보류⛔ / 완료👍 |
| 목표 | select | 📖ㅣ학습 / 📋ㅣ기획 / 🔎ㅣ조사 / 💻ㅣ구현 / 💀ㅣ수정 / 🥵ㅣ테스트 및 검증 |
| 중요도 | select | ⭐⭐⭐⭐⭐ / ⭐⭐⭐ / ⭐ |
| 담당자 | person | array of user ids |
| 날짜 | date | date:날짜:start / date:날짜:end / date:날짜:is_datetime |
| 완료 | checkbox | |
| 상위 업무 | relation | -> same data source |
| 하위 할 일 | relation | -> same data source (auto) |
| 업무 로그 | relation | -> that project's 업무 로그 data source |

## 업무 로그 data source — schema

| property | type | notes |
|---|---|---|
| 내용 | title | required, keep to one line |
| 작성자 | person | |
| 날짜 | date | |
| 관련 업무 | relation | -> that project's 업무 data source |

## 자료실 folder pattern (identical across every copy)
- one shared page "문서 폴더(전체가 읽어야하는 것)" containing six sub-pages matching 목표: 학습 문서 / 기획 문서 / 조사 문서 / 구현 문서 / 수정 내역 문서 / 테스트 및 검증 문서
- one plain page per team member, titled with just their real name — no prefix/suffix

## What a concrete instance looks like

Every copy of the template resolves to this shape — the IDs below are placeholders, not real ones. Fetch the current project's own database each time to get its actual values:

**"<프로젝트 이름>"** (page `<page id>`):
- 업무: `collection://<업무 data source id>`
- 업무 로그: `collection://<업무 로그 data source id>`
- workspace user ids for 담당자/작성자: `<user id>` per person
- 자료실 personal folders: one page per member, e.g. 홍길동, 김영희, 이철수

A second copy of the template (a different team project) has the *same* property names and option values but an entirely different set of IDs — which is why nothing here is ever hardcoded.

## Useful queries (substitute the current project's actual collection URL)

Find in-progress 업무:
```sql
SELECT url, "이름" FROM "collection://<업무 id>" WHERE "진행 상황" = '진행 중🏃‍♀️'
```

Keyword search on 업무 name:
```sql
SELECT url, "이름" FROM "collection://<업무 id>" WHERE "이름" LIKE '%키워드%'
```

## Creating pages
Use notion-create-pages with parent `{"type": "data_source_id", "data_source_id": "<id from that project's collection:// url>"}` for 업무/업무 로그 entries, or `{"type": "page_id", "page_id": "<자료실 page id>"}` for personal folder pages.
