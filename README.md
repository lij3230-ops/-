# 김비서 워크숍 실습 — 완전 가이드

> 카페 운영 전용 비서 웹 시스템을 HTML + CSS + JS만으로 구현하고 GitHub에 올리는 전체 과정 정리.  
> 이 문서를 따라가면 처음부터 똑같이 재현할 수 있습니다.

---

## 목차

1. [프로젝트 개요](#1-프로젝트-개요)
2. [최종 파일 구조](#2-최종-파일-구조)
3. [단계별 작업 내용](#3-단계별-작업-내용)
   - Step 1: 데이터 파악
   - Step 2: 기본 페이지 2개 생성
   - Step 3: 디자인 고급화 (글래스모피즘 + 다크모드)
   - Step 4: 카페 테마 적용
   - Step 5: 회의록 페이지 생성
   - Step 6: 파일 분류 정리
   - Step 7: 차트 페이지 생성
   - Step 8: SVG 다이어그램 생성
   - Step 9: 사이트 분석 보고서 생성
   - Step 10: 내비게이션 메뉴 추가
   - Step 11: 뒤로 가기 버튼 추가
   - Step 12: GitHub 업로드
4. [핵심 코드 패턴](#4-핵심-코드-패턴)
5. [GitHub 업로드 방법 (매번 사용)](#5-github-업로드-방법-매번-사용)
6. [다음 번 체크리스트](#6-다음-번-체크리스트)

---

## 1. 프로젝트 개요

| 항목 | 내용 |
|------|------|
| 프로젝트명 | 김비서 (카페 전용 비서 웹 시스템) |
| 사용 기술 | HTML, CSS, JavaScript (외부 라이브러리 없음) |
| 페이지 수 | 6개 (index, dashboard, chart, diagram, meeting-result, report) |
| 디자인 | 글래스모피즘 + 다크/라이트 모드 + 카페 테마 |
| GitHub 레포 | https://github.com/lij3230-ops/- |

---

## 2. 최종 파일 구조

```
워크숍-실습/
├── index.html              # 소개 메인 페이지 (CTA → dashboard.html)
├── dashboard.html          # 업무 대시보드 (4개 섹션 + 상단 내비)
├── chart.html              # 매출 분석 차트 (선그래프 + 막대그래프)
├── diagram.svg             # 업무 프로세스 다이어그램 (5단계)
├── meeting-result.html     # 회의 결과 보고서 (인쇄 가능)
├── report.html             # 사이트 분석 보고서
├── .env.local              # 환경변수 파일 (토큰 등) ← Git 제외
├── .gitignore              # Git 추적 제외 목록
├── 김비서-데이터/
│   ├── 매출데이터.csv
│   ├── 업무목록.csv
│   ├── 주간일정.txt
│   ├── 프로젝트현황.csv
│   └── 회의록.txt
└── 정리해줘/
    ├── 보고서/  (4개 파일)
    ├── 메모/    (3개 파일)
    ├── 업무/    (4개 파일)
    └── 기타/    (4개 파일)
```

---

## 3. 단계별 작업 내용

---

### Step 1: 데이터 파악

데이터 파일을 먼저 모두 읽어 내용을 파악합니다.

**파일 목록 및 주요 내용:**

| 파일 | 내용 |
|------|------|
| `매출데이터.csv` | 1~2월 매출 30건, 1월 합계 46,499,000원 / 2월 24,507,000원 |
| `업무목록.csv` | 10개 업무, 컬럼: 업무/우선순위/상태/담당자/마감일/카테고리 |
| `주간일정.txt` | 2026년 3월 10~14일 요일별 일정 |
| `프로젝트현황.csv` | 6개 프로젝트, 진행률 10%~80% |
| `회의록.txt` | 마케팅팀 주간회의, 참석자 4명, 액션아이템 6개 |

**포인트:** 차트 페이지 등 데이터를 직접 표현할 때는 CSV를 런타임에 파싱하지 않고, 미리 계산한 값을 JS 배열로 하드코딩합니다.

---

### Step 2: 기본 페이지 2개 생성

#### `index.html` — 소개 메인 페이지
- 상단: 프로젝트 소개 문구
- 중단: 기능 카드 3개 (할 일 관리 / 일정 관리 / 매출 분석)
- 하단: "내 대시보드 보기" 버튼 → `dashboard.html` 링크

#### `dashboard.html` — 메인 대시보드
- 4개 카드 2×2 그리드 레이아웃:
  1. **할 일 목록** — `업무목록.csv` 기반, 우선순위 배지
  2. **이번 주 일정** — `주간일정.txt` 기반, 요일별 목록
  3. **프로젝트 진행률** — `프로젝트현황.csv` 기반, 프로그레스 바
  4. **매출 요약** — `매출데이터.csv` 기반, 수치 카드

**공통 기본 스타일:**
```css
body { font-family: 'Apple SD Gothic Neo', 'Pretendard', sans-serif; }
.card { background: #fff; border-radius: 16px; padding: 24px; box-shadow: 0 4px 16px rgba(0,0,0,0.08); }
```

---

### Step 3: 디자인 고급화 (글래스모피즘 + 다크모드)

#### 글래스모피즘 핵심 CSS
```css
.card {
  background: rgba(255, 252, 240, 0.62);
  backdrop-filter: blur(22px) saturate(200%);
  -webkit-backdrop-filter: blur(22px) saturate(200%);
  border: 1px solid rgba(245, 210, 140, 0.65);
  box-shadow: 0 8px 32px rgba(180,100,20,0.10), 0 2px 8px rgba(0,0,0,0.04);
  border-radius: 24px;
}
```

#### CSS 변수로 테마 관리
```css
:root {
  /* 라이트 모드 */
  --bg-body: linear-gradient(145deg, #fff8f0 0%, #fef3e2 45%, #fff0f5 100%);
  --glass: rgba(255,252,240,0.62);
  --glass-border: rgba(245,210,140,0.65);
  --t1: #2d1a08;   /* 제목 색 */
  --t2: #78614a;   /* 부제 색 */
  --a1: #d97706;   /* 강조 색 */
  --a-grad: linear-gradient(135deg, #f59e0b, #d97706);
}

[data-theme="dark"] {
  --bg-body: #1a0f05;
  --glass: rgba(255,230,170,0.05);
  --glass-border: rgba(255,200,110,0.12);
  --t1: #f5e6d3;
  --a1: #fbbf24;
  --a-grad: linear-gradient(135deg, #fbbf24, #f59e0b);
}
```

#### FOUC 방지 (테마 깜빡임 제거)
`<head>` 맨 앞에 인라인 스크립트로 테마를 즉시 적용합니다:
```html
<script>
  (function(){
    const t = localStorage.getItem('theme') || 'light';
    document.documentElement.setAttribute('data-theme', t);
  })();
</script>
```

#### 토글 버튼 HTML + JS
```html
<!-- HTML -->
<button class="theme-toggle" id="themeToggle">
  <div class="toggle-thumb" id="toggleThumb">☀️</div>
</button>

<!-- JS -->
<script>
  const toggle = document.getElementById('themeToggle');
  const thumb  = document.getElementById('toggleThumb');
  function applyTheme(t) {
    document.documentElement.setAttribute('data-theme', t);
    thumb.textContent = t === 'dark' ? '☀️' : '🌙';
  }
  applyTheme(localStorage.getItem('theme') || 'light');
  toggle.addEventListener('click', () => {
    const next = document.documentElement.getAttribute('data-theme') === 'dark' ? 'light' : 'dark';
    localStorage.setItem('theme', next);
    applyTheme(next);
  });
</script>
```

```css
/* 토글 버튼 CSS */
.theme-toggle {
  position: fixed; top: 20px; right: 20px; z-index: 200;
  width: 58px; height: 30px; border-radius: 15px;
  border: 1px solid var(--glass-border);
  background: var(--toggle-bg); cursor: pointer;
  display: flex; align-items: center; padding: 3px;
}
.toggle-thumb {
  width: 22px; height: 22px; border-radius: 50%;
  background: var(--a-grad);
  transition: transform 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
}
[data-theme="dark"] .toggle-thumb { transform: translateX(28px); }
```

---

### Step 4: 카페 테마 적용

#### 떠다니는 빵 이모지 장식
```html
<div class="bread b1">🥐</div>
<div class="bread b2">☕</div>
<div class="bread b3">🧁</div>
<div class="bread b4">🍩</div>
<div class="bread b5">🍞</div>
<!-- ... b10까지 -->
```

```css
.bread {
  position: fixed; pointer-events: none; z-index: 1;
  opacity: var(--bread-op);  /* 라이트: 0.22, 다크: 0.12 */
}
.b1 { font-size: 46px; top: 6%; left: 4%; transform: rotate(-18deg);
      animation: bf 7s ease-in-out infinite; }

@keyframes bf {
  0%, 100% { filter: blur(0); }
  50% { translate: 0 -10px; }
}
```

#### 배경 Orb 효과
```css
.orb { position: fixed; border-radius: 50%; filter: blur(90px); pointer-events: none; }
.orb-1 { width: 650px; height: 650px;
          background: radial-gradient(circle, var(--orb1), transparent 70%);
          top: -220px; right: -180px;
          animation: drift1 18s ease-in-out infinite; }
```

---

### Step 5: 회의록 페이지 생성 (`meeting-result.html`)

`회의록.txt`를 읽어 인쇄 가능한 보고서 HTML로 변환합니다.

**페이지 구조:**
```
doc-header (보라 그라디언트 헤더)
  └─ 제목 + 기본 정보 (날짜/참석자/주관)
doc-body
  ├─ 회의 기본 정보 섹션
  ├─ 논의 내용 요약 (불릿)
  └─ 액션 아이템 테이블 (담당자 | 할 일 | 기한 | 우선순위)
```

**인쇄용 CSS:**
```css
@media print {
  body { background: #fff; padding: 0; }
  .back-btn { display: none; }  /* 뒤로가기 버튼 숨김 */
  .doc-header { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
}
```

---

### Step 6: 파일 분류 정리 (PowerShell)

`정리해줘` 폴더 내 파일들을 용도별로 하위 폴더에 이동합니다.

```powershell
# 폴더 생성
New-Item -ItemType Directory -Path "정리해줘\보고서", "정리해줘\메모", "정리해줘\업무", "정리해줘\기타"

# 예시: 보고서 파일 이동
Move-Item "정리해줘\보고서_*.txt" "정리해줘\보고서\"
Move-Item "정리해줘\메모.txt"     "정리해줘\메모\"
```

---

### Step 7: 차트 페이지 생성 (`chart.html`)

외부 라이브러리 없이 SVG + JS로 차트를 직접 구현합니다.

#### 선 그래프 — SVG Bezier 곡선
```javascript
const ns = 'http://www.w3.org/2000/svg';

function makePath(points) {
  let d = `M ${points[0].x} ${points[0].y}`;
  for (let i = 1; i < points.length; i++) {
    const prev = points[i - 1], curr = points[i];
    const cx = (prev.x + curr.x) / 2;
    d += ` C ${cx} ${prev.y}, ${cx} ${curr.y}, ${curr.x} ${curr.y}`;
  }
  return d;
}

const path = document.createElementNS(ns, 'path');
path.setAttribute('d', makePath(data));
path.setAttribute('stroke', '#3b82f6');
path.setAttribute('fill', 'none');
path.setAttribute('stroke-width', '2.5');
```

#### 막대 그래프 — CSS 애니메이션
```html
<div class="bar-wrap">
  <div class="bar" data-pct="0.72"></div>
</div>
```
```css
.bar {
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.8s ease;
}
```
```javascript
// 페이지 로드 후 애니메이션 실행
document.querySelectorAll('.bar').forEach(bar => {
  const pct = parseFloat(bar.dataset.pct);
  bar.style.width = '100%';
  requestAnimationFrame(() => {
    bar.style.transform = `scaleX(${pct})`;
  });
});
```

---

### Step 8: SVG 다이어그램 생성 (`diagram.svg`)

**캔버스 크기:** 800 × 200px  
**5단계 박스 배치:** x = 40, 190, 340, 490, 640 (너비 120, 간격 30)

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="800" height="200" viewBox="0 0 800 200">

  <defs>
    <!-- 화살표 마커 -->
    <marker id="arr" markerWidth="9" markerHeight="7" refX="8" refY="3.5" orient="auto">
      <path d="M0,0.5 L0,6.5 L8,3.5 Z" fill="#a0aec0"/>
    </marker>
    <!-- 점 마커 -->
    <marker id="dot" markerWidth="6" markerHeight="6" refX="3" refY="3" orient="auto">
      <circle cx="3" cy="3" r="2.5" fill="#a0aec0"/>
    </marker>
  </defs>

  <!-- 배경 -->
  <rect width="800" height="200" fill="#f8fafc" rx="14"/>

  <!-- 박스 (예: Step 1 파란색) -->
  <rect x="40" y="60" width="120" height="80" rx="18" fill="#dbeafe" stroke="#93c5fd" stroke-width="1.8"/>

  <!-- 번호 배지 -->
  <circle cx="152" cy="68" r="10" fill="#3b82f6"/>
  <text x="152" y="72.5" text-anchor="middle" dominant-baseline="middle"
        font-size="10" font-weight="800" fill="#fff">1</text>

  <!-- 단계 텍스트 -->
  <text x="100" y="105" text-anchor="middle" dominant-baseline="middle"
        font-size="24" font-weight="900" fill="#1e40af">기획</text>
  <text x="100" y="128" text-anchor="middle"
        font-size="11" font-weight="600" fill="#60a5fa" letter-spacing="1">Plan</text>

  <!-- 화살표 -->
  <line x1="165" y1="100" x2="187" y2="100"
        stroke="#a0aec0" stroke-width="2"
        marker-start="url(#dot)" marker-end="url(#arr)"/>

</svg>
```

**단계별 파스텔 색상표:**

| 단계 | 배경 | 테두리 | 텍스트 | 배지 |
|------|------|--------|--------|------|
| 기획 | `#dbeafe` | `#93c5fd` | `#1e40af` | `#3b82f6` |
| 제작 | `#dcfce7` | `#86efac` | `#14532d` | `#22c55e` |
| 검토 | `#fef9c3` | `#fde047` | `#713f12` | `#ca8a04` |
| 배포 | `#ffedd5` | `#fdba74` | `#7c2d12` | `#f97316` |
| 분석 | `#ede9fe` | `#c4b5fd` | `#4c1d95` | `#8b5cf6` |

---

### Step 9: 사이트 분석 보고서 생성 (`report.html`)

외부 사이트를 분석해 HTML 보고서로 정리합니다.

**페이지 구조:**
```
page-header (파란 그라디언트, 사이트 기본 정보)
grid-2 (카드 2열 그리드)
  ├─ 서비스 소개
  ├─ 사업자 정보
  ├─ 페이지 구조
  └─ 주요 기능
grid-3 (카드 3열 그리드)
  ├─ 디자인 분석
  ├─ 잘한 점
  └─ 개선점
score-grid (6개 지표 점수)
report-footer
```

---

### Step 10: 내비게이션 메뉴 추가 (`dashboard.html`)

```html
<nav class="nav-bar">
  <button class="nav-tab active">📊 대시보드</button>
  <div class="nav-sep"></div>
  <a href="meeting-result.html" class="nav-tab">📋 회의록</a>
  <a href="chart.html"          class="nav-tab">📈 매출 현황</a>
  <a href="diagram.svg"         class="nav-tab">🔄 업무 프로세스</a>
  <a href="report.html"         class="nav-tab">🔍 사이트 분석</a>
</nav>
```

```css
.nav-bar {
  display: flex; align-items: center; gap: 4px;
  background: var(--glass);
  backdrop-filter: blur(20px);
  border: 1px solid var(--glass-border);
  border-radius: 16px;
  padding: 6px 8px;
  margin-bottom: 28px;
}
.nav-tab {
  padding: 8px 16px; border-radius: 10px;
  font-size: 13px; font-weight: 700;
  text-decoration: none; color: var(--t2);
  border: none; background: transparent; cursor: pointer;
  transition: background 0.2s, color 0.2s;
}
.nav-tab.active,
.nav-tab:hover {
  background: var(--a-grad);
  color: #fff;
}
```

---

### Step 11: 뒤로 가기 버튼 추가

각 하위 페이지에 대시보드로 돌아가는 버튼을 추가합니다.

**HTML/CSS 페이지 (meeting-result.html, report.html):**
```html
<!-- <body> 바로 아래 -->
<a href="dashboard.html" class="back-btn">← 대시보드</a>
```

```css
.back-btn {
  display: inline-flex; align-items: center; gap: 6px;
  position: fixed; top: 16px; left: 16px; z-index: 100;
  background: rgba(99,102,241,0.85);   /* 페이지 테마 색으로 변경 */
  backdrop-filter: blur(10px);
  color: rgba(255,255,255,0.92);
  font-size: 13px; font-weight: 700;
  padding: 8px 16px; border-radius: 999px;
  text-decoration: none;
  border: 1px solid rgba(255,255,255,0.22);
  transition: background 0.2s, transform 0.2s;
}
.back-btn:hover { transform: translateX(-2px); }

@media print { .back-btn { display: none; } }
```

**SVG 파일 (diagram.svg):**
```svg
<style>
  .back-btn rect { transition: fill 0.18s; }
  .back-btn:hover rect { fill: #c4b5fd; }
</style>

<a href="dashboard.html" class="back-btn">
  <rect x="10" y="8" width="108" height="26" rx="13"
        fill="#ede9fe" stroke="#c4b5fd" stroke-width="1.2"/>
  <text x="64" y="21" text-anchor="middle" dominant-baseline="middle"
        font-size="11" font-weight="700" fill="#6d28d9">← 대시보드</text>
</a>
```

---

### Step 12: GitHub 업로드

#### 12-1. .gitignore 생성 (먼저 반드시!)
```
# 환경 변수 / 시크릿
.env
.env.local
.env*.local

# Claude Code 로컬 설정
.claude/

# OS 임시 파일
.DS_Store
Thumbs.db

# 에디터 설정
.vscode/
.idea/
```

#### 12-2. GitHub Token 준비
1. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. **Generate new token** 클릭
3. 권한: `repo` 체크 → 생성
4. 생성된 토큰(`ghp_...`)을 바탕화면의 `token.txt`에 저장
5. 프로젝트 폴더의 `.env.local`에 `GITHUB_TOKEN=ghp_...` 형식으로 저장

#### 12-3. Git 초기화 및 Push
```powershell
# 프로젝트 폴더로 이동
Set-Location "프로젝트 폴더 경로"

# Git 초기화
git init
git config user.email "내이메일@gmail.com"
git config user.name "GitHub사용자명"

# 파일 추가 및 커밋
git add .
git commit -m "Initial commit: 프로젝트명"

# 원격 저장소 연결 및 Push (토큰 사용)
$token = (Get-Content ".env.local" | Where-Object { $_ -match "^GITHUB_TOKEN=" }) -replace "^GITHUB_TOKEN=", ""
git remote add origin "https://GitHub사용자명:$token@github.com/GitHub사용자명/레포명.git"
git branch -M main
git push -u origin main

# 토큰을 URL에서 제거 (보안)
git remote set-url origin "https://github.com/GitHub사용자명/레포명.git"
```

#### 12-4. 이후 수정사항 Push (매번)
```powershell
Set-Location "프로젝트 폴더 경로"
git add .
git commit -m "수정 내용 요약"

$token = (Get-Content ".env.local" | Where-Object { $_ -match "^GITHUB_TOKEN=" }) -replace "^GITHUB_TOKEN=", ""
git remote set-url origin "https://GitHub사용자명:$token@github.com/GitHub사용자명/레포명.git"
git push
git remote set-url origin "https://github.com/GitHub사용자명/레포명.git"
```

---

## 4. 핵심 코드 패턴

### 숫자 천 단위 쉼표
```javascript
function fmt(n) { return n.toLocaleString('ko-KR') + '원'; }
// 예: fmt(1234567) → "1,234,567원"
```

### 우선순위 배지
```css
.badge { padding: 2px 8px; border-radius: 999px; font-size: 11px; font-weight: 700; }
.badge.high   { background: #fee2e2; color: #dc2626; }
.badge.medium { background: #fef9c3; color: #ca8a04; }
.badge.low    { background: #dcfce7; color: #16a34a; }
```

### 프로그레스 바 애니메이션
```html
<div class="proj-bar"><div class="proj-fill" data-pct="0.75"></div></div>
```
```javascript
document.querySelectorAll('.proj-fill').forEach(el => {
  const pct = parseFloat(el.dataset.pct);
  setTimeout(() => { el.style.width = (pct * 100) + '%'; }, 100);
});
```

### 그라디언트 텍스트
```css
.hl {
  background: var(--a-grad);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

---

## 5. GitHub 업로드 방법 (매번 사용)

> 새 프로젝트를 시작할 때마다 이 순서를 따릅니다.

```
[준비]
1. GitHub에서 새 레포지토리 생성 (빈 레포)
2. Personal Access Token 발급 (권한: repo)
3. 프로젝트 폴더에 .gitignore 생성 → .env.local 반드시 포함

[첫 업로드]
4. git init
5. git config user.email / user.name
6. git add .  →  git commit  →  git push (토큰 URL 방식)
7. push 직후 remote URL에서 토큰 제거

[이후 업데이트]
8. git add .  →  git commit  →  git push (동일 방식)
```

---

## 6. 다음 번 체크리스트

### 페이지 만들 때
- [ ] `:root` CSS 변수 먼저 정의
- [ ] `<head>`에 FOUC 방지 스크립트 삽입
- [ ] 다크/라이트 모드 양쪽 확인
- [ ] `@media print` 블록 추가 (보고서 페이지)
- [ ] 뒤로 가기 버튼 추가 (`position: fixed; top: 16px; left: 16px`)

### GitHub 올릴 때
- [ ] `.gitignore`에 `.env.local` 포함 확인
- [ ] `git ls-files .env.local` 실행 → 결과 없어야 함 (추적 안 됨)
- [ ] push 후 remote URL 토큰 제거 확인
- [ ] GitHub 페이지에서 `.env.local`이 없는지 최종 확인

### 보안
- [ ] 토큰은 절대 코드에 직접 쓰지 않기
- [ ] 토큰은 `.env.local`에만 저장 (`.gitignore`로 제외)
- [ ] `token.txt`는 바탕화면에만 보관, 프로젝트 폴더에 두지 않기

---

*마지막 업데이트: 2026년 5월 28일*
