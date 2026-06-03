# CA BW Analyzer

> NR CA Bandwidth Combination 분석 도구 — 3GPP TS 38.101-1 V19.5.0 기반

![Version](https://img.shields.io/badge/version-2.0-3B5BDB)
![Standard](https://img.shields.io/badge/3GPP-TS%2038.101--1%20V19.5.0-10b981)
![Offline](https://img.shields.io/badge/동작방식-완전%20오프라인-f59e0b)
![License](https://img.shields.io/badge/license-MIT-8888AA)

5G NR CA 조합에서 허용되는 대역폭(BW) 조합을 자동으로 분석하는 완전 오프라인 웹 도구입니다.
CA 조합을 입력하면 3GPP 표준 기반 DB에서 즉시 조회하여 Pareto 최적 BW 조합 목록을 출력합니다.

---

## 주요 기능

- **즉시 분석** — CA 조합 입력 후 수십 ms 내 결과 출력
- **완전 오프라인** — 인터넷 연결 불필요, 서버 없이 브라우저에서 바로 동작
- **3,250개 CA 조합 DB** — 3GPP TS 38.101-1 V19.5.0 기반
- **Pareto 최적 필터** — Cross-case Pareto로 최적 조합만 자동 추출
- **FDD / TDD / Intra-band 지원** — 모든 CA 구성 유형 처리
- **설정 커스터마이징** — 칩셋 Limitation 및 단말 BW 직접 수정 가능
- **결과 복사** — 줄 클릭 복사 / 전체 복사 지원

---

## 빠른 시작

### 1. 파일 다운로드

```
ca-bw-analyzer/
├── index.html       ← 메인 앱
└── ca_bw_db.js      ← CA 조합 DB (~444KB)
```

두 파일을 **같은 폴더**에 저장하세요.

### 2. 실행

`index.html` 파일을 브라우저에서 열면 바로 실행됩니다.

```
# 예) 파일 탐색기에서 index.html 더블클릭
# 또는 브라우저 주소창에 파일 경로 입력
```

### 3. GitHub Pages로 접속

```
https://{유저명}.github.io/{저장소명}/
```

---

## 사용 방법

### 입력 표기법

| 표기 | 의미 | 예시 |
|------|------|------|
| `nXA` | 단일 CC (1CC) | `n1A`, `n78A` |
| `nXB` | FDD Intra 2CC | `n3B`, `n7B` |
| `nXC` | TDD Intra 2CC | `n78C`, `n77C` |
| `nX(2A)` | Intra 2CC | `n78(2A)`, `n77(2A)` |
| `nX(3A)` | Intra 3CC | `n77(3A)` |

여러 밴드는 하이픈(`-`)으로 연결합니다.

### 입력 예시

```
# FDD only
n1A-n3A-n28A
n1A-n3A-n7A-n28A

# FDD + TDD
n1A-n78A
n1A-n3A-n78C
n1A-n3A-n78(2A)
n1A-n40A-n77(2A)

# FDD Intra 포함
n1A-n3(2A)-n7A
n7B-n28A

# 5CA 이상
n1A-n3A-n7A-n78(2A)   → BCS5 : 3F+2T
```

### 결과 형식

```
n1A[20M]-n3A[40M]-n78C[90+90M](BCS0)
n1A[20M]-n3A[40M]-n78C[80+100M](BCS0)
...
```

| 특수 결과 | 의미 |
|-----------|------|
| `No Limitation` | 칩셋 제한 이내 — 모든 BW 조합 사용 가능 |
| `(BCS4 and 5)` | BCS4/5 전용 조합 |
| `BCS5 : {key}` | 5CA 이상 — DB 탐색 생략, 구성 정보만 표시 |

---

## 지원 범위

### FDD 밴드

| 밴드 | 지원 BW (MHz) |
|------|--------------|
| n1, n2, n3, n25 | 5 / 10 / 15 / 20 / 25 / 30 / 40 |
| n5, n8, n26 | 5 / 10 / 15 / 20 |
| n7 | 5 / 10 / 15 / 20 / 25 / 30 / 40 |
| n12 | 5 / 10 / 15 |
| n20 | 5 / 10 / 15 / 20 |
| n28 | 5 / 10 / 15 / 20 / 25 / 30 |
| n66 | 5 / 10 / 15 / 20 / 30 / 40 |
| n71 | 5 / 10 / 15 / 20 |
| n75 | 5 / 10 / 15 / 20 / 25 / 30 / 40 / 50 |

### TDD 밴드

| 밴드 | 지원 BW (MHz) |
|------|--------------|
| n38 | 10 / 15 / 20 / 30 / 40 |
| n40, n41, n77, n78 | 10 / 15 / 20 / 30 / 40 / 50 / 60 / 70 / 80 / 90 / 100 |

### 칩셋 Limitation 기본값

| 구성 | FDD Max | TDD Max |
|------|---------|---------|
| 1F / 2F / 3F / 4F | 50 / 80 / 100 / 100 MHz | — |
| 1T / 2T / 3T | — | 100 / 200 / 300 MHz |
| 1F+1T | 50 MHz | 100 MHz |
| 2F+1T | 80 MHz | 100 MHz |
| 1F+2T | 50 MHz | 200 MHz |
| 2F+2T | 50+200 / 60+180 / 80+140 MHz | (3케이스) |
| 3F+1T | 100 MHz | 100 MHz |
| 1F+3T (HI) | 40 MHz | 220 MHz |
| 1F+3T (LO) | 30 MHz | 240 MHz |

> **1F+3T 기준**: FDD 단말 UE Max ≥ 40 MHz → HI / ≤ 30 MHz → LO

---

## 설정 패널

헤더 우측 **⚙ 설정** 버튼을 클릭하면 펼쳐집니다.

| 탭 | 기능 |
|----|------|
| 칩셋 Limitation | FDD/TDD Max BW 직접 수정 / 케이스 추가·삭제 |
| 단말 BW | 밴드별 지원 BW 수정 (쉼표 구분 입력) |

**초기값 복원** 버튼으로 3GPP 기본값으로 즉시 리셋할 수 있습니다.

---

## 파일 구조

```
ca-bw-analyzer/
├── index.html          # 메인 분석기 (HTML + JS 로직 통합)
├── ca_bw_db.js         # CA 조합 DB (3,250개 / ~444KB)
├── workflow.html        # 단계별 워크플로우
├── flowchart.html       # 동작 순서도 (SVG)
└── manual.pdf          # 사용 매뉴얼
```

---

## 문서

| 문서 | 설명 |
|------|------|
| [워크플로우](workflow.html) | 12단계 분석 프로세스 상세 설명 |
| [순서도](flowchart.html) | SVG 기반 동작 순서도 (분기 포함) |
| [매뉴얼](manual.pdf) | 배포용 사용 매뉴얼 (8챕터) |

---

## 기술 스택

- **Frontend** — Vanilla HTML / CSS / JavaScript (프레임워크 없음)
- **DB** — JSON → JS 변환 / O(1) Map 조회
- **알고리즘** — 재귀 완전탐색 / Cross-case Pareto / Cartesian 전개
- **호스팅** — GitHub Pages

---

## 기반 표준

**3GPP TS 38.101-1 V19.5.0**
NR; User Equipment (UE) radio transmission and reception; Part 1: Range 1 Standalone

---

## 지원 브라우저

Chrome / Edge / Firefox / Safari 최신 버전 권장
Internet Explorer(IE)는 지원하지 않습니다.
