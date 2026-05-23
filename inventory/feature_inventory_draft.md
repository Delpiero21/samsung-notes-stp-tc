# Samsung Notes — 기능 인벤토리 (초안)

> **목적**: "Notes 에 무슨 기능이 있는가" 의 단일 진실. 모든 후속 시나리오 / TC 의 출발점.
>
> **상태**: 초안 (draft). 다음 3 source 통합 필요:
> 1. ✅ **실측 데이터** — game-explorer 가 5분 budget 으로 463 화면 발견 (resource-id 풍부)
> 2. ⏳ **UX 가이드 / Samsung Notes 매뉴얼** 분석 (PROJECT.md §1.5)
> 3. ⏳ **QA / 검증자 인터뷰** (자주 쓰는 시나리오 + 우선순위)
>
> **마감**: 5월 1주차 안에 정식판 (`feature_inventory.md`) 으로 승격
>
> **버전**: draft-v0.1 (2026-05-20) — Notes One UI 8.5 / S26 기준

---

## 0. 범주 별 기능 총수 (초안)

| # | 범주 | 기능 수 | 우선순위 |
|---|---|---:|---|
| 1 | 노트 lifecycle (생성/편집/저장/삭제) | 6 | ⭐⭐⭐ |
| 2 | 폴더 관리 | 6 | ⭐⭐⭐ |
| 3 | 텍스트 편집 (키보드 + 서식) | 11 | ⭐⭐⭐ |
| 4 | 펜툴 (drawing) | 12 | ⭐⭐⭐ |
| 5 | 미디어 삽입 | 7 | ⭐⭐ |
| 6 | 노트 메타 (제목 / 즐겨찾기 / 잠금) | 5 | ⭐⭐ |
| 7 | 검색 / 정렬 / 필터 | 5 | ⭐⭐ |
| 8 | 공유 / 내보내기 | 5 | ⭐⭐ |
| 9 | AI 어시스트 | 4 | ⭐⭐⭐ (Galaxy AI 차별점) |
| 10 | 설정 / 환경 | 8 | ⭐ |
| 11 | 동기화 / 백업 | 4 | ⭐ |
| 12 | 휴지통 / 복원 | 3 | ⭐⭐ |
| 13 | 위젯 / 진입 경로 | 4 | ⭐ |
| 14 | 노트 스타일 / 배경 | 3 | ⭐ |
| 15 | 멀티 페이지 | 3 | ⭐ |
| **합계** | | **86** | |

→ 86 ± 20 (정확한 수는 정식판에서 확정)

---

## 1. 노트 lifecycle (6)

| ID | 기능 | resource-id (실측 / 추정) | precondition |
|---|---|---|---|
| F001 | 새 노트 생성 (✏ FAB) | `create_note_btn` | Notes 메인 화면 |
| F002 | 노트 편집 (텍스트 / 펜) | EditText + canvas | 노트 열린 상태 |
| F003 | 노트 저장 (자동 / 수동) | `action_save` | 변경된 노트 |
| F004 | 노트 삭제 | options menu → 삭제 | 노트 선택 또는 열림 |
| F005 | 다중 선택 + 일괄 삭제 | long press → checkbox | 노트 list |
| F006 | 노트 복제 | options menu → 복제 (추정) | 노트 열림 |

## 2. 폴더 관리 (6)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F010 | 폴더 생성 | drawer → 새 폴더 (추정) | Notes 메인 |
| F011 | 폴더 안 노트 보기 | `sub_folder_list_item` 클릭 | 폴더 ≥ 1개 |
| F012 | 폴더 이름 변경 | long press → 이름 변경 (추정) | 폴더 ≥ 1개 |
| F013 | 폴더 삭제 | long press → 삭제 | 폴더 ≥ 1개 |
| F014 | 노트 폴더 간 이동 | options menu → 이동 | 노트 ≥ 1개 + 폴더 ≥ 2개 |
| F015 | 폴더 트리 view (햄버거 메뉴) | `nav menu` | Notes 메인 |

## 3. 텍스트 편집 (11)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F020 | 키보드 텍스트 입력 (한국어) | EditText + 한글 IME | 노트 편집 화면 |
| F021 | 키보드 텍스트 입력 (영어) | EditText + 영어 IME | 노트 편집 화면 |
| F022 | 체크리스트 추가 | `richtext_todoBtn` | 노트 편집 |
| F023 | 텍스트 색상 변경 (9종) | `richtext_textColorBtn` + popup | 텍스트 선택 |
| F024 | 글자 배경색 변경 | `richtext_textBgColorBtn` | 텍스트 선택 |
| F025 | 텍스트 형식 (헤더 H1/H2/...) | `richtext_text_format` | 노트 편집 |
| F026 | 폰트 크기 변경 (15 default) | `richtext_font_size` | 텍스트 선택 |
| F027 | 굵게 (Bold) | `richtext_boldBtn` | 텍스트 선택 |
| F028 | 기울이기 (Italic) | `richtext_italicBtn` | 텍스트 선택 |
| F029 | 밑줄 (Underline) | `richtext_underlineBtn` | 텍스트 선택 |
| F030 | 클립보드 붙여넣기 | EditText long press → 붙여넣기 | 클립보드에 텍스트 존재 |

## 4. 펜툴 / Drawing (12) — Galaxy AI 차별점

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F040 | 펜 모드 진입 | `hw_toolbar_pen` | 노트 편집 |
| F041 | 펜 종류 변경 (5종: 만년필/펜/마커/색연필/형광펜) | popup `hw_toolbar_pen_type` | 펜 모드 + 펜 다시 클릭 |
| F042 | 펜 색상 변경 (9종 기본 + 5 페이지) | popup palette | 펜 모드 + 펜 다시 클릭 |
| F043 | 펜 굵기 변경 (1~60, default 30) | popup slider | 펜 모드 + 펜 다시 클릭 |
| F044 | 형광펜 모드 | `hw_toolbar_highlighter` | 노트 편집 |
| F045 | 지우개 모드 (영역/획) | `hw_toolbar_eraser` | 펜 그림 ≥ 1획 |
| F046 | 선택 모드 (영역 선택) | `hw_toolbar_selection` | 펜 그림 ≥ 1획 |
| F047 | 텍스트 모드 (변환) | `hw_toolbar_text_mode` | 펜 그림 |
| F048 | 노트 어시스트 (AI) | `toolbar_ai_menu` | One UI 8.5 (Galaxy AI 활성) |
| F049 | 되돌리기 (Undo) | `hw_toolbar_undo` | 액션 ≥ 1개 |
| F050 | 다시 하기 (Redo) | `hw_toolbar_redo` | undo 후 |
| F051 | 즐겨찾는 펜 등록 (★) | popup 좌상단 별표 | 펜 모드 popup |

## 5. 미디어 삽입 (7)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F060 | 이미지 삽입 | options → 이미지 | 노트 편집 |
| F061 | 카메라 촬영 후 삽입 | options → 카메라 | 카메라 권한 |
| F062 | 음성 메모 녹음 | options → 음성 | 마이크 권한 |
| F063 | 파일 첨부 | options → 파일 | 노트 편집 |
| F064 | PDF 가져오기 | `import_pdf_container` | 노트 메인 |
| F065 | 노트 스타일 적용 | `note_style_container` | 노트 편집 |
| F066 | 캡처 / 도형 삽입 | options → 도형 (추정) | 노트 편집 |

## 6. 노트 메타 (5)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F070 | 제목 설정 | `composer_toolbar_title` | 노트 편집 |
| F071 | 즐겨찾기 (별 표시) | options menu (추정) | 노트 ≥ 1개 |
| F072 | 노트 잠금 (PIN/지문) | options menu (추정) | 노트 ≥ 1개 + 잠금 권한 |
| F073 | 알림 / 리마인더 | options menu (추정) | 노트 ≥ 1개 |
| F074 | 읽기 모드 (편집 비활성) | `action_read_mode` | 노트 열림 |

## 7. 검색 / 정렬 / 필터 (5)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F080 | 검색 (한국어) | `action_search` | Notes 메인 |
| F081 | 검색 (영어) | `action_search` | Notes 메인 |
| F082 | 검색 (필기 텍스트 OCR 포함) | `action_search` | 손글씨 노트 ≥ 1개 |
| F083 | 정렬 (수정일 / 생성일 / 제목) | options menu (추정) | 노트 ≥ 2개 |
| F084 | 카테고리 필터 (즐겨찾기만 등) | drawer 필터 (추정) | 노트 ≥ 1개 |

## 8. 공유 / 내보내기 (5)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F090 | 공유 다이얼로그 (취소만, 실제 전송 X — 안전) | options → 공유 | 노트 ≥ 1개 |
| F091 | PDF 로 내보내기 | options → 내보내기 → PDF | 노트 ≥ 1개 |
| F092 | 이미지 (PNG) 로 내보내기 | options → 내보내기 → 이미지 | 노트 ≥ 1개 |
| F093 | Microsoft Word 로 내보내기 | options → 내보내기 → Word (옵션) | 노트 ≥ 1개 |
| F094 | 텍스트 (.txt) 로 내보내기 | options → 내보내기 → 텍스트 | 노트 ≥ 1개 |

## 9. AI 어시스트 (4) — Galaxy AI 차별점

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F100 | 노트 어시스트 popup 진입 | `toolbar_ai_menu` | Galaxy AI 활성 단말 (One UI 8.5+) |
| F101 | 자동 형식 (수기 → 깔끔한 텍스트) | AI popup | 손글씨 ≥ 1줄 |
| F102 | 요약 (Summary) | AI popup → 요약 | 텍스트 ≥ N 자 |
| F103 | 번역 | AI popup → 번역 | 텍스트 ≥ 1줄 |

## 10. 설정 / 환경 (8)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F110 | 설정 메뉴 진입 | drawer → 설정 (또는 ⋮) | Notes 메인 |
| F111 | 동기화 켜기 / 끄기 | 설정 → 동기화 | Samsung Account 로그인 |
| F112 | 자동 저장 간격 | 설정 → 저장 | 설정 진입 |
| F113 | 잠금 화면에서 새 노트 작성 허용 | 설정 → 잠금화면 | 설정 진입 |
| F114 | 손글씨 → 텍스트 자동 변환 옵션 | 설정 → 손글씨 | 설정 진입 |
| F115 | 노트 가져오기 (다른 앱에서) | 설정 → 가져오기 | 설정 진입 |
| F116 | 보안 폴더로 이동 | 설정 → 보안 | 보안 폴더 활성 단말 |
| F117 | 정보 / 버전 | 설정 → 정보 | 설정 진입 |

## 11. 동기화 / 백업 (4)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F120 | Samsung Cloud 동기화 (수동) | 설정 → 동기화 → 지금 동기화 | Samsung Account |
| F121 | 자동 동기화 (Wi-Fi 만) | 설정 → 동기화 → Wi-Fi only | Samsung Account |
| F122 | 동기화 충돌 해결 (sync conflict) | dialog 발생 시 | 두 단말에서 같은 노트 편집 |
| F123 | 백업 / 복원 (수동) | 설정 → 백업 | Samsung Account |

## 12. 휴지통 / 복원 (3)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F130 | 휴지통 진입 | drawer → 휴지통 | drawer 열림 |
| F131 | 휴지통에서 복원 | 휴지통 → 노트 long press → 복원 | 휴지통에 노트 ≥ 1개 |
| F132 | 휴지통 비우기 (영구 삭제) | 휴지통 → 모두 삭제 | 휴지통에 노트 ≥ 1개 |

## 13. 위젯 / 진입 경로 (4)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F140 | 홈 위젯에서 새 노트 작성 | 홈 위젯 long press 추가 | 홈 화면 |
| F141 | S Pen Air Command 에서 노트 작성 | S Pen 분리 → Air Command | S Pen 단말 |
| F142 | Always On Display 에서 빠른 메모 | AOD 더블탭 (추정) | AOD 활성 |
| F143 | Quick Settings 타일에서 진입 | quick settings 타일 (옵션) | 타일 추가됨 |

## 14. 노트 스타일 / 배경 (3)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F150 | 노트 배경 변경 (분홍/노랑 등) | `note_style_container` | 노트 편집 |
| F151 | 라인 / 그리드 표시 | 노트 스타일 → 라인 | 노트 편집 |
| F152 | 페이지 방향 (세로/가로) | options → 방향 (추정) | 노트 편집 |

## 15. 멀티 페이지 (3)

| ID | 기능 | resource-id | precondition |
|---|---|---|---|
| F160 | 페이지 추가 | "+" 아이콘 (펜 모드) | 펜 모드 |
| F161 | 페이지 indicator (1/N) | "1/N" 표시 | 페이지 ≥ 2 |
| F162 | 페이지 삭제 / 복제 | options → 페이지 (추정) | 페이지 ≥ 2 |

---

## 16. 미확정 / 추가 조사 필요

다음은 실측/매뉴얼/QA 인터뷰로 확정 필요:

- [ ] 위젯 종류 정확 list (홈 위젯 / lockscreen / quick settings)
- [ ] S Pen Air Command 메뉴 안 Notes 진입 시나리오
- [ ] 손글씨 → 텍스트 OCR 정확도 평가 (별도 케이스 가능성)
- [ ] AI 어시스트 의 전체 기능 (요약 / 번역 / 자동 형식 외 추가 있나)
- [ ] 보안 폴더 / Samsung Knox 연계
- [ ] 다른 자사앱 연계 (Reminder 변환 / Calendar 일정 등)
- [ ] Bixby Routine 자동 노트 작성
- [ ] DeX 모드에서 동작 (별도 UI)

---

## 17. 우선순위 부여 — TC 작성 순서

⭐⭐⭐ (Top — 5월 첫 2주 TC 화 목표):
- F001 (새 노트 생성), F002 (편집), F003 (저장)
- F020 (한국어 키보드 입력), F027 (Bold)
- F040 (펜 모드), F042 (펜 색상 9종 — Case 4 hard case)
- F080 (검색)
- F100 (AI 어시스트 진입)
- F010 (폴더 생성)

⭐⭐ (Mid — 5월 후반):
- F004 (삭제) + F131 (휴지통 복원)
- F090 (공유 — 안전 차단 시연용)
- F060 (이미지 삽입)
- F070 (제목 설정)

⭐ (Bottom — 6월 이후):
- F114~F117 (설정)
- F120~F123 (동기화)
- F140~F143 (위젯)

---

## 18. 갱신 정책

- 실측 데이터 (game-explorer events.jsonl) 분석 후 자동 보강
- QA 인터뷰 결과 반영 후 우선순위 재조정
- Samsung Notes 빌드 업데이트 시 새 기능 추가
- 본 문서가 **모든 TC 의 source of truth**. TC 파일 첫 줄에 해당 F-id 명시.

```python
"""
TC042 — F042 (펜 색상 9종 클릭)
inventory: docs/inventory/feature_inventory.md#F042
precondition: F001 (새 노트 1개) + F040 (펜 모드) 통과
"""
```
