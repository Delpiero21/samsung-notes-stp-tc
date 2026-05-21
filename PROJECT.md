# PROJECT — Samsung Notes STP Test Cases

> **사내 AI 도구가 이 문서만 읽고 동일한 작업을 재현/계속할 수 있도록 작성된 단일 진실 문서.**
> Samsung Notes 앱의 시나리오별 STP 테스트 스크립트 프로젝트.

---

## 0. TL;DR (한 줄)

> Samsung Notes 의 사용자 시나리오를 **STP (사내 UI dump 기반 scripting tool)** 로 자동화한
> Python TC (Test Case) 모음. **TC0 = Notes 진입**이 모든 후속 TC 의 전제.

---

## 1. 프로젝트 개요

### 1.1 무엇을 하는 프로젝트인가

Samsung Notes 앱의 각 UI flow (새 노트 / 펜툴 / 텍스트 서식 / 폴더 / 검색 등) 를 시나리오별
**Python 테스트 스크립트 (`TC*.py`)** 로 작성한다. 각 TC 는 STP 의 UI dump + adb input
명령 조합으로 단말을 자동 조작하고 결과를 보고한다.

### 1.2 왜 만드는가 — 사내 mandate

| 요구 | 정합성 |
|---|---|
| 100% 자율 탐색 도구 X — 사람이 검증한 시나리오를 자동 재생 | ✅ 시나리오별 TC |
| 사외 AI 도구 활용 OK + 사외 데이터 유출 차단 | ✅ STP + adb local only |
| 사내 도구 (STP) 활용 | ✅ STP 가 단말 조작 표준 |
| 결과 audit 가능 | ✅ 각 TC 의 log + screenshot + dump XML |
| One UI 8.5 / S26 타겟 | ✅ Galaxy SM-S947U1 |

### 1.3 자율 탐색 도구와의 분업

| 도구 | 영역 | 강점 |
|---|---|---|
| **자율 탐색** (DroidBot/Kea2/Fastbot) | 발견 — "도달 가능한 모든 UI 매핑" | 알려지지 않은 화면 발견 |
| **본 프로젝트 (STP TC)** | 검증 — "사람이 정의한 시나리오 100회 재생" | 회귀 / 안정성 / 재현성 |

본 프로젝트는 검증자가 손으로 한 번 시나리오를 정의 → STP 로 100회 자동 재생 → 회귀 검증.

---

## 2. 기술 스택

### 2.1 핵심 도구

| 항목 | 도구 |
|---|---|
| **언어** | Python 3.10+ |
| **단말 자동화** | **STP** (사내 UI dump 기반 scripting tool) |
| **단말 통신** | adb (Android Debug Bridge) |
| **단말** | Galaxy S26 (One UI 8.5), 검증용 |
| **CI** | (선택) 사내 CI 시스템 + scheduled 실행 |

### 2.2 STP 사용 모델 (개념적)

STP 의 정확한 API 는 사내 매뉴얼 참조. 본 문서는 일반 UI dump 기반 scripting tool 의 흐름과
유사하다고 가정 — `dump → parse XML → element 찾기 → tap/swipe/input → 결과 확인`.

```
┌─────────────────────────────────────────────────┐
│ Python TC (TC0.py / TC1.py / ...)               │
│   ├─ STP 모듈 import                             │
│   ├─ dump_screen() → XML                        │
│   ├─ find_by_resource_id(...)                   │
│   ├─ tap(x, y) / tap_element(elem)              │
│   ├─ swipe / input_text                         │
│   ├─ wait_for_element(timeout=10)               │
│   └─ assert / screenshot / log                  │
└─────────────────────────────────────────────────┘
              │
              │ adb shell uiautomator dump / input tap / ...
              ▼
        [Galaxy 단말]
```

### 2.3 외부 의존

**0 — 사내 STP + 표준 Python + adb 만**.
- `pip install` 외부 패키지 추가 시 사내 PyPI mirror 확인
- 사외 LLM API 호출 X

---

## 3. 핵심 결정 — TC 구조

### 3.1 TC0 = 모든 TC 의 전제

**TC0.py — Samsung Notes 앱 진입**
- 단말 잠금 화면 깨우기 (필요 시)
- `com.samsung.android.app.notes` 실행
- Notes 메인 화면 (폴더 list) 로딩 대기
- ✅ "폴더 / 노트 N개" 텍스트 확인 → 진입 성공 판정

**왜 분리?**
- TC1, TC2, ... 의 매번 진입 코드 중복 제거
- Notes 진입 자체가 실패할 수 있어 별도 검증 필요
- TC0 통과 = "Notes 가 정상 동작" 자체 검증
- TC1+ 의 시작점이 항상 동일한 상태 보장

### 3.2 후속 TC (시나리오별)

| TC | 시나리오 | 핵심 검증 |
|---|---|---|
| **TC0** | Samsung Notes 진입 | 메인 화면 로딩 |
| **TC1** | 새 노트 생성 + 저장 | 노트 생성 + list 에 추가됨 |
| **TC2** | 펜툴 모드 + 색상 9종 클릭 | 펜 색상 팔레트 popup (Case 4) |
| **TC3** | 텍스트 입력 + 글자 서식 (B/I/U) | 키보드 + 글자 서식 toolbar |
| **TC4** | 폴더 생성 + 노트 이동 | 폴더 / 노트 이동 |
| **TC5** | 검색 (한국어 + 영어) | 검색 결과 list |
| **TC6** | 노트 삭제 + 휴지통 복원 | 삭제 / 복원 flow |
| **TC7** | 노트 공유 (취소만, 실제 전송 X) | 공유 다이얼로그 표시 |
| **TC8** | AI 어시스트 popup 진입 | Case 5 (AI 어시스트 자체 popup) |
| ... | (확장 가능) | |

### 3.3 TC 실행 모드

**단독 실행**:
```bash
python tc/TC0.py    # Notes 진입만
python tc/TC1.py    # TC0 자동 호출 후 TC1
```

**일괄 실행**:
```bash
python scripts/run_all.py            # TC0~TCN 순차
python scripts/run_all.py --only TC1,TC2,TC3   # 일부
```

각 TC 의 첫 줄:
```python
from tc.TC0 import enter_samsung_notes
enter_samsung_notes()   # 진입 보장
# ... 시나리오 시작 ...
```

---

## 4. 프로젝트 구조

```
samsung-notes-stp-tc/
│
├── PROJECT.md                # ⭐ 본 문서
├── README.md                 # 사용자 안내 (간단)
├── requirements.txt          # Python 의존 (STP 외)
│
├── tc/                       # ⭐ Test Cases (1 파일 = 1 시나리오)
│   ├── __init__.py
│   ├── TC0.py                # Samsung Notes 진입 (공통)
│   ├── TC1.py                # 새 노트 생성 + 저장
│   ├── TC2.py                # 펜툴 색상 9종
│   ├── TC3.py                # 텍스트 + B/I/U
│   ├── TC4.py                # 폴더 + 이동
│   ├── TC5.py                # 검색
│   ├── TC6.py                # 삭제 + 복원
│   ├── TC7.py                # 공유 (취소)
│   └── TC8.py                # AI 어시스트
│
├── common/                   # 공통 헬퍼
│   ├── __init__.py
│   ├── stp_wrapper.py        # STP API 추상화 (dump, tap, swipe, find_*)
│   ├── notes_helpers.py      # Notes 전용 helper (펜툴 좌표, 색상 list 등)
│   ├── assertions.py         # 검증 helper (assert_element_present, ...)
│   └── logger.py             # TC 통합 로깅
│
├── data/                     # 정적 데이터
│   ├── expected/             # 기대 dump XML (golden file)
│   │   ├── TC0_main_screen.xml
│   │   ├── TC2_pen_palette.xml
│   │   └── ...
│   └── fixtures/             # 시나리오 입력 데이터
│       └── sample_texts.json
│
├── reports/                  # ⭐ TC 실행 결과 (gitignore)
│   └── {timestamp}/
│       ├── summary.json      # PASS/FAIL/SKIP 집계
│       ├── TC0_pass.log
│       ├── TC0_before.png
│       ├── TC0_after.png
│       ├── TC1_pass.log
│       └── ...
│
├── scripts/                  # 헬퍼 스크립트
│   ├── run_all.py            # TC 일괄 실행
│   ├── adb_setup.sh          # adb reverse / device check
│   └── clean_device.py       # Notes 초기화 (테스트 데이터 정리)
│
└── .gitignore                # reports/, __pycache__/, .vscode/, ...
```

---

## 5. 주요 데이터 모델

### 5.1 UI dump XML (STP 출력 — 가정)

`adb shell uiautomator dump` 와 유사한 형태로 가정:

```xml
<?xml version='1.0' encoding='UTF-8' standalone='yes' ?>
<hierarchy rotation="0">
  <node index="0" text="" resource-id="" class="android.widget.FrameLayout"
        package="com.samsung.android.app.notes" content-desc=""
        checkable="false" checked="false" clickable="false" enabled="true"
        focusable="false" focused="false" scrollable="false"
        long-clickable="false" password="false" selected="false"
        bounds="[0,0][1080,2340]">
    <node text="폴더" resource-id="com.samsung.android.app.notes:id/collapsing_appbar_extended_title"
          class="android.widget.TextView" bounds="[0,295][1080,411]" .../>
    <node text="" resource-id="com.samsung.android.app.notes:id/create_note_btn"
          class="android.widget.ImageButton" content-desc="노트 작성"
          clickable="true" bounds="[841,1966][999,2124]" .../>
    ...
  </node>
</hierarchy>
```

### 5.2 TC 실행 결과 (`reports/{timestamp}/summary.json`)

```json
{
  "run_id": "20260520_093024",
  "device": { "model": "SM-S947U1", "sdk": 35 },
  "started_at": 1747432800123,
  "ended_at": 1747432980456,
  "duration_sec": 180.3,
  "total": 9,
  "passed": 7,
  "failed": 1,
  "skipped": 1,
  "tcs": [
    { "id": "TC0", "status": "pass", "duration_ms": 4521,
      "screenshots": ["TC0_before.png", "TC0_after.png"], "asserts": 3 },
    { "id": "TC1", "status": "pass", "duration_ms": 8210, "asserts": 5 },
    { "id": "TC2", "status": "fail", "duration_ms": 12450,
      "reason": "펜 색상 팔레트 popup 안 뜸 — find_by_id 'hw_palette' timeout",
      "screenshots": ["TC2_failed.png"] },
    { "id": "TC3", "status": "skip", "reason": "TC2 의존 실패로 skip" },
    ...
  ]
}
```

### 5.3 TC 파일 표준 구조

```python
# tc/TC1.py
"""TC1 — 새 노트 생성 + 저장.

전제: TC0 통과 후 Notes 메인 화면.
종료 상태: Notes 메인 화면 (생성한 노트 list 에 표시).
"""
from __future__ import annotations

import logging
from common.stp_wrapper import dump_screen, find_by_resource_id, tap_element, wait_for_element
from common.notes_helpers import NOTES_PKG
from common.assertions import assert_element_present
from common.logger import tc_logger
from tc.TC0 import enter_samsung_notes

log = tc_logger("TC1")


def run() -> bool:
    """TC1 본체. 반환 True = pass, False = fail."""
    # 1. 전제 — Notes 진입
    if not enter_samsung_notes():
        log.error("TC0 (Notes 진입) 실패 → TC1 skip")
        return False

    # 2. "새 노트 작성" 버튼 클릭
    dump = dump_screen()
    create_btn = find_by_resource_id(dump, "com.samsung.android.app.notes:id/create_note_btn")
    if create_btn is None:
        log.error("새 노트 작성 버튼 못 찾음")
        return False
    tap_element(create_btn)

    # 3. 편집 화면 로딩 대기
    if not wait_for_element(
        resource_id="com.samsung.android.app.notes:id/composer_toolbar_title",
        timeout=10,
    ):
        log.error("편집 화면 로딩 실패")
        return False

    # 4. 텍스트 입력 (placeholder — STP API 에 맞춰 조정)
    # input_text("TC1 자동 생성 노트")

    # 5. 저장 버튼 클릭
    dump = dump_screen()
    save_btn = find_by_resource_id(dump, "com.samsung.android.app.notes:id/action_save")
    assert_element_present(save_btn, "저장 버튼 부재")
    tap_element(save_btn)

    # 6. 메인 화면 복귀 확인
    if not wait_for_element(
        resource_id="com.samsung.android.app.notes:id/collapsing_appbar_extended_title",
        timeout=10,
    ):
        log.error("저장 후 메인 화면 복귀 실패")
        return False

    log.info("TC1 PASS")
    return True


if __name__ == "__main__":
    import sys
    sys.exit(0 if run() else 1)
```

---

## 6. TC 작성 가이드

### 6.1 황금 규칙

1. **TC0 의존**: 모든 TC 의 시작 = `enter_samsung_notes()`
2. **종료 상태 보장**: TC 종료 시 다음 TC 를 위해 깔끔한 상태 (Notes 메인 또는 진입 가능 상태)
3. **idempotent**: 같은 TC 를 100번 돌려도 같은 결과 (테스트 데이터는 매번 새로 만들거나 사전 정리)
4. **assertion 명시**: `assert_element_present`, `assert_text_equals` 등으로 fail 사유 명확
5. **screenshot 필수**: 시나리오 시작 / 종료 / 실패 시점 자동 캡처
6. **timeout 명시**: 모든 `wait_for_*` 는 timeout 명시 (default 10초)

### 6.2 element 식별 우선순위

1. **resource-id** (Samsung Notes 의 `com.samsung.android.app.notes:id/*`) — 가장 안정적
2. **content-desc** (한국어 라벨 "노트 작성", "저장" 등) — 다국어 단말에서 영문화 risk
3. **bounds 좌표** — 마지막 수단. 해상도 변경 시 깨짐

### 6.3 새 TC 추가 체크리스트

```
□ tc/TCN.py 작성 (위 5.3 의 표준 구조)
□ enter_samsung_notes() 가 첫 줄
□ 시나리오 종료 시 Notes 메인 화면으로 BACK 처리
□ 시나리오 정의를 docstring 에 명시 (전제 / 종료 상태 / 검증 항목)
□ data/expected/TCN_*.xml (필요 시 golden file)
□ scripts/run_all.py 에 자동 등록 (또는 tc/ 디렉토리 자동 스캔이면 불필요)
□ PROJECT.md 의 §3.2 TC 명세 표에 row 추가
```

---

## 7. 공통 헬퍼 (common/)

### 7.1 `common/stp_wrapper.py`

STP API 를 일관된 인터페이스로 wrapping. 의사 코드 (실제 STP API 에 맞춰 구현):

```python
"""STP API 추상화. 사내 STP 매뉴얼에 맞춰 구현체 교체."""
from __future__ import annotations

from typing import Any


def dump_screen() -> "ElementTree":
    """현재 화면 dump 후 XML tree 반환.

    실제 구현 예 (STP API 사용):
        return stp.uidump()
    또는 adb 직접:
        run("adb shell uiautomator dump /sdcard/window_dump.xml")
        run("adb pull /sdcard/window_dump.xml")
        return ET.parse("window_dump.xml")
    """
    ...


def find_by_resource_id(dump, resource_id: str) -> "Element | None":
    """resource-id 로 element 검색. 못 찾으면 None."""
    ...


def find_by_content_desc(dump, content_desc: str) -> "Element | None":
    """content-desc 한국어 라벨로 검색."""
    ...


def tap_element(elem) -> None:
    """element 의 bounds 중심 좌표를 탭. STP 가 element 직접 지원하면 STP API 사용."""
    ...


def tap(x: int, y: int) -> None:
    """좌표 직접 탭. fallback 용."""
    ...


def swipe(x1: int, y1: int, x2: int, y2: int, duration_ms: int = 300) -> None:
    ...


def input_text(text: str) -> None:
    """포커스된 EditText 에 텍스트 입력. STP 또는 adb shell input text 사용."""
    ...


def press_back() -> None: ...
def press_home() -> None: ...


def wait_for_element(
    resource_id: str | None = None,
    content_desc: str | None = None,
    text: str | None = None,
    timeout: float = 10.0,
    poll_interval: float = 0.5,
) -> bool:
    """주어진 element 가 나타날 때까지 polling. True = 발견 / False = timeout."""
    ...


def take_screenshot(out_path: str) -> None:
    """현재 화면 PNG 저장. STP screenshot 또는 adb shell screencap."""
    ...
```

### 7.2 `common/notes_helpers.py`

Samsung Notes 전용 상수 + helper:

```python
NOTES_PKG = "com.samsung.android.app.notes"

# 자주 쓰는 resource-id
RID_MAIN_TITLE = f"{NOTES_PKG}:id/collapsing_appbar_extended_title"   # "폴더"
RID_CREATE_BTN = f"{NOTES_PKG}:id/create_note_btn"                    # 새 노트 작성
RID_SAVE_BTN = f"{NOTES_PKG}:id/action_save"                          # 저장
RID_TOOLBAR_PEN = f"{NOTES_PKG}:id/hw_toolbar_pen"                    # 펜 모드
RID_TOOLBAR_HIGHLIGHTER = f"{NOTES_PKG}:id/hw_toolbar_highlighter"
RID_RICHTEXT_BOLD = f"{NOTES_PKG}:id/richtext_boldBtn"
RID_RICHTEXT_ITALIC = f"{NOTES_PKG}:id/richtext_italicBtn"
RID_RICHTEXT_UNDERLINE = f"{NOTES_PKG}:id/richtext_underlineBtn"
# ... (확장)

# 9 색상 swatch — Case 4 (a11y label 없는 영역)
PEN_COLOR_COUNT = 9
```

### 7.3 `common/logger.py`

표준 로깅 + reports/{timestamp}/ 자동 저장:

```python
import logging
from datetime import datetime
from pathlib import Path

_REPORT_ROOT = Path("reports")


def _run_dir() -> Path:
    # 같은 run 안에서 모든 TC 가 공유 — 환경변수 또는 모듈 전역
    ...


def tc_logger(tc_id: str) -> logging.Logger:
    """TC 별 logger. reports/{run}/TCx.log 로 자동 저장."""
    ...
```

---

## 8. 빌드 / 실행

### 8.1 사전 조건

- **PC**: Windows (또는 Linux/macOS), Python 3.10+, adb
- **사내 STP**: 사내 매뉴얼에 따라 설치 + 환경변수 / config 설정
- **단말**: Galaxy S26 (One UI 8.5), USB debugging ON
- **Samsung Notes**: 단말에 사전 설치 (preload 또는 store)

### 8.2 1회 셋업

```bash
git clone https://github.com/Delpiero21/samsung-notes-stp-tc.git
cd samsung-notes-stp-tc
python -m venv .venv
.venv\Scripts\activate    # Windows
pip install -r requirements.txt
# 사내 STP 추가 설치 (사내 매뉴얼)
```

### 8.3 단말 확인

```bash
adb devices
# Galaxy SM-S947U1 → device 출력 확인
```

### 8.4 TC 실행

```bash
# 단독
python tc/TC0.py
python tc/TC1.py

# 일괄
python scripts/run_all.py

# 일부만
python scripts/run_all.py --only TC0,TC1,TC2
```

### 8.5 결과 확인

```bash
ls reports/$(ls reports | tail -1)/
# summary.json + TCx_*.log + *.png
```

---

## 9. 결과 보고 모델

### 9.1 단일 TC 의 PASS/FAIL 판정

- **PASS**: 모든 assert 통과 + 종료 상태 정상
- **FAIL**: 1개 이상 assert 실패 또는 timeout
- **SKIP**: 의존 TC 실패 (예: TC0 fail → TC1~TC8 skip)

### 9.2 한 run 의 종합 (summary.json)

§5.2 의 schema. 사내 보고에 그대로 활용 가능.

### 9.3 회귀 비교

같은 단말 빌드 / 다른 단말 빌드 사이의 PASS 비율 비교:

```bash
python scripts/compare_runs.py reports/20260520_093024 reports/20260521_142133
# → 어떤 TC 가 새로 fail 됐는지, regression 발생 여부
```

---

## 10. 사내 AI 도구 재현 가이드

본 문서를 받은 사내 AI 도구는 다음 작업을 동일하게 수행할 수 있어야 한다.

### 10.1 새 TC 추가 task 받았을 때

예: *"Samsung Notes 의 '제목 변경' 시나리오 TC9 를 추가해줘"*

1. **시나리오 정의 확인 / 작성**
   - 전제: TC0 통과 후 Notes 메인
   - 단계: 첫 노트 long-press → 메뉴 → "이름 변경" → 텍스트 입력 → 저장
   - 종료 상태: Notes 메인 (변경된 이름 표시)
   - 검증: 새 이름이 list 에 있는가
2. **resource-id 수집**
   - 실제 단말에서 uiautomator dump → 관련 element 의 id 확인
   - `common/notes_helpers.py` 의 상수에 추가
3. **`tc/TC9.py` 작성** (§5.3 표준 구조)
4. **`PROJECT.md` 의 §3.2 표에 row 추가**
5. **`scripts/run_all.py` 확인** — tc/ 자동 스캔이면 불필요, 명시적이면 추가
6. **단독 실행 검증** — `python tc/TC9.py`
7. **일괄 회귀 실행** — `python scripts/run_all.py` 으로 TC0~TC9 모두 PASS 확인

### 10.2 기존 TC 실패 디버깅

1. `reports/{latest}/TCx_fail.log` 확인 — 실패 line + reason
2. `reports/{latest}/TCx_failed.png` 확인 — 실패 시점 화면
3. 단말에서 같은 단계 손으로 재현 → UI 변경 여부 확인
4. `common/notes_helpers.py` 의 resource-id 가 새 빌드에 유효한지 점검
5. fix 후 단독 실행 → 일괄 회귀

### 10.3 새 단말 추가

1. `adb devices` 확인
2. `common/stp_wrapper.py` 의 device serial 환경변수화 (기본 `$ANDROID_SERIAL`)
3. `python scripts/run_all.py --serial RXXXXXX` 으로 명시
4. 단말별 결과 비교 → 단말 호환성 보고

---

## 11. 사내 보안 / mandate 정합성

| 항목 | 정합성 |
|---|---|
| 외부 LLM API 호출 | ❌ 0 — Python 로컬 + STP + adb 만 |
| 사외 데이터 송신 | ❌ 0 — reports/ 는 PC 안에만 |
| 외부 npm/pip 의존 | 최소 — requirements.txt 의 사내 허용 패키지만 |
| 단말 화면 raw data | reports/ 안 PC. 외부 절대 X |
| **STP** | ✅ 사내 표준 도구 |

---

## 12. 현재 진행 상황 (2026-05-20 기준)

### 12.1 완료

- ✅ PROJECT.md 작성 (본 문서)
- ✅ 디렉토리 구조 결정
- ✅ TC0 시나리오 정의 (Samsung Notes 진입)

### 12.2 다음 단계 (우선순위 순)

1. **`common/stp_wrapper.py`** — STP API wrapping 구체 구현 (사내 매뉴얼 참조)
2. **`tc/TC0.py`** — Samsung Notes 진입 + 진입 성공 판정
3. **`tc/TC1.py`** — 새 노트 생성 + 저장 (가장 단순)
4. **`tc/TC2.py`** — 펜툴 색상 9종 (Case 4 핵심)
5. **`scripts/run_all.py`** — 일괄 실행
6. **`reports/` 자동 생성 + summary.json**
7. **TC3~TC8** 차례로 추가

### 12.3 확장 가능성

- **다국어 단말** — `find_by_content_desc` 가 영문 라벨 단말에서도 동작하게 i18n map
- **단말 fleet 회귀** — Galaxy A / S / Z 시리즈 동시 회귀
- **CI 통합** — 사내 CI 에서 nightly run
- **메트릭 dashboard** — 시간순 PASS 비율 시각화

---

## 13. 참고

| 자료 | 용도 |
|---|---|
| 사내 STP 매뉴얼 | STP API 정확한 사용법 |
| Samsung Notes 의 a11y resource-id list | element 식별 |
| `adb shell uiautomator dump` 출력 | element 발견 / 시나리오 설계 |
| Android `pm list packages` / `dumpsys activity activities` | 단말 상태 확인 |

---

## 14. 라이선스 / 보안

- 사내 사용. 외부 배포 시 별도 검토.
- 외부 의존 0 — Python 표준 + 사내 STP + adb only.
- 결과 데이터는 PC 안 (`reports/`) 만 유지.
