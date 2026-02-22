# 시각화 계획서 — 데이터 분석 에이전틱 수업

> **Single Source of Truth**: 이 문서는 프로젝트의 모든 시각화 요소를 정의합니다.
> 챕터 생성 시 이 문서를 참조하여 일관된 시각화를 삽입합니다.

---

## 1. 시각화 유형 분류

이 프로젝트는 4가지 시각화 유형을 사용합니다:

| 유형 | 기술 | 용도 | 인터랙션 |
|------|------|------|----------|
| **A. Plotly.js 차트** | HTML iframe (Plotly CDN) | 데이터 시각화, 분석 결과 | 호버, 줌, 슬라이더, 토글 |
| **B. Mermaid 다이어그램** | MkDocs 코드 블록 | 프로세스 흐름, 구조도 | 없음 (정적) |
| **C. HTML 인터랙티브** | HTML iframe (CSS/JS) | 상황카드 뒤집기, 데이터 테이블 | 클릭, 정렬, 필터 |
| **D. CSS 미리보기** | Markdown 내 HTML+CSS | 루브릭, 체크리스트, 비교 카드 | 없음 (정적) |

---

## 2. Plotly.js 데모 목록 (유형 A)

### 2.1 공통 템플릿

모든 Plotly HTML 파일은 이 템플릿을 기반으로 합니다:

```html
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<script src="https://cdn.plot.ly/plotly-2.35.2.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;600;700&display=swap" rel="stylesheet">
<style>
  * { margin:0; padding:0; box-sizing:border-box; }
  html,body { width:100%; height:100%; font-family:'Noto Sans KR',sans-serif; background:#fff; }
  #chart { width:100%; height:100%; }
</style>
</head>
<body>
<div id="chart"></div>
<script>
// Plotly.js 코드
</script>
</body>
</html>
```

### 2.2 데모 파일 상세

| # | 파일명 | 챕터 | 내용 | 차트 유형 | 인터랙션 |
|---|--------|------|------|-----------|----------|
| 1 | `hero_dashboard.html` | index | 4분할 대시보드 (산점도+막대+파이+추세) | subplot 4종 | 호버 |
| 2 | `ch04_bad_vs_good.html` | ch04 | 나쁜 지시 결과 vs 좋은 지시 결과 비교 | 이중 subplot | 버튼 토글 |
| 3 | `ch05_scatter_sleep.html` | ch05 | 수면시간 vs 피로도 산점도 (추세선 포함) | scatter + trendline | 호버+줌 |
| 4 | `ch05_bar_weekday.html` | ch05 | 요일별 학교생활 만족도 막대그래프 | bar | 호버 |
| 5 | `ch05_outlier_slider.html` | ch05,ch06 | **이상치 제거 효과 실시간 시각화** | scatter + slider | **슬라이더+애니메이션** |
| 6 | `ch05_correlation_heatmap.html` | ch05 | 7개 변수 상관계수 히트맵 | heatmap | 호버 |
| 7 | `ch06_trend_analysis.html` | ch06 | 연도별 추세 + 회귀선 | scatter + line | 호버+줌 |
| 8 | `ch06_group_comparison.html` | ch06 | 수면시간 구간별 성적 박스플롯 | box | 호버 |
| 9 | `ch06_verification_demo.html` | ch06 | 검증 전/후 분석 결과 비교 | 이중 subplot | 버튼 토글 |
| 10 | `ch08_peer_review_viz.html` | ch08 | 피어리뷰 평가 항목별 레이더 차트 | scatterpolar | 호버 |
| 11 | `ch09_project_scatter.html` | ch09 | 독립 프로젝트 예시: 운동 vs 스트레스 | scatter | 호버+줌 |
| 12 | `ch09_project_bar.html` | ch09 | 독립 프로젝트 예시: 카테고리별 비교 | bar (grouped) | 호버 |
| 13 | `situation_cards.html` | ch02,ch03 | 6종 상황카드 뒤집기 인터랙티브 | **유형 C** | 클릭 뒤집기 |
| 14 | `data_preview_school.html` | ch05 | 학교 설문 데이터 미리보기 테이블 | **유형 C** | 정렬+스크롤 |
| 15 | `data_preview_public.html` | ch05 | 서울 기온 공공데이터 미리보기 | **유형 C** | 정렬+스크롤 |

### 2.3 핵심 데모: ch05_outlier_slider.html

이 프로젝트의 **플래그십 인터랙티브 데모**. VPython 프로젝트의 3D 장면에 해당하는 역할.

**개념**: 이상치를 제거하면 상관관계가 어떻게 변하는지 슬라이더로 실시간 체험

```
[슬라이더: 이상치 임계값]  ◀━━━━━━━●━━━━━━━▶
                          1.5σ   2.0σ   3.0σ (전체)

  ┌──────────────────────────────────────┐
  │  산점도: 스마트폰 사용시간 vs 수면시간  │
  │                                      │
  │  ●  ●●  ●                             │
  │   ●●●●●●●●                            │
  │    ●●●●●●●●●●                         │
  │     ●●●●●●●●●●●                       │
  │      ●●●●                ◎ (이상치)     │
  │                                      │
  │  r = -0.45 → 슬라이더 조정 → r = -0.78 │
  └──────────────────────────────────────┘
```

**구현 방식**: Plotly.js `Plotly.react()` + HTML range input
- 슬라이더 값에 따라 데이터 필터링
- 추세선 실시간 재계산
- 상관계수 annotation 실시간 업데이트
- 이상치 점은 빨간색 X 마커로 표시

---

## 3. Mermaid 다이어그램 목록 (유형 B)

Mermaid는 MkDocs 코드 블록으로 직접 삽입합니다.

| # | 챕터 | 내용 | Mermaid 유형 |
|---|------|------|-------------|
| 1 | ch01 | 4차시 전체 흐름도 | flowchart LR |
| 2 | ch01 | 4C 역량 매핑 | mindmap |
| 3 | ch02 | 3단 질문 변환 프로세스 | flowchart TD |
| 4 | ch02 | 1차시 수업 흐름 타임라인 | flowchart LR |
| 5 | ch04 | 2차시 수업 흐름 | flowchart LR |
| 6 | ch04 | Soul Document 작성 워크플로우 | flowchart TD |
| 7 | ch06 | 5단계 검증 프로세스 | flowchart TD |
| 8 | ch06 | 지침서 개선 반복 사이클 | flowchart LR |
| 9 | ch07 | 피어리뷰 로테이션 (World Café) | flowchart LR |
| 10 | ch07 | 3차시 수업 흐름 | flowchart LR |
| 11 | ch09 | 독립 프로젝트 파이프라인 | flowchart TD |
| 12 | ch09 | 4차시 타임라인 + 체크포인트 | flowchart LR |
| 13 | ch10 | 시리즈 전체 구조 종합도 | flowchart TD |

---

## 4. HTML 인터랙티브 목록 (유형 C)

### 4.1 situation_cards.html — 상황카드 6종

CSS 3D 뒤집기(perspective + rotateY) 구현.

| # | 영역 | 이모지 | 색상 | 앞면 (문제 상황) | 뒷면 (분석 질문 예시) |
|---|------|--------|------|-----------------|---------------------|
| 1 | 학교생활 | 🏫 | #4361ee | "우리 학교 학생들이 잠을 못 자는 것 같아요" | 학년별 수면시간 차이가 있는가? |
| 2 | 환경 | 🌍 | #2ec4b6 | "여름이 점점 더 더워지는 것 같아요" | 서울 여름 평균기온이 10년간 실제로 올랐는가? |
| 3 | 건강 | 💪 | #e63946 | "스마트폰을 많이 하면 피곤한 것 같아요" | 스마트폰 사용시간과 피로도 사이에 상관관계가 있는가? |
| 4 | 경제 | 💰 | #f4a261 | "용돈이 항상 부족한 것 같아요" | 고등학생 월평균 용돈과 지출 패턴은 어떤가? |
| 5 | 문화 | 🎭 | #9b5de5 | "요즘 다들 같은 노래만 듣는 것 같아요" | 연령대별 음악 장르 선호도에 차이가 있는가? |
| 6 | 기술 | 💻 | #00b4d8 | "AI가 모든 걸 대신해줄 것 같아요" | 고등학생의 AI 활용 빈도와 학습 효과는 관련이 있는가? |

### 4.2 data_preview_*.html — 데이터 테이블

간단한 HTML 테이블 + 정렬 기능 (바닐라 JS).

---

## 5. CSS 컴포넌트 목록 (유형 D)

Markdown 내에서 HTML+CSS 클래스로 스타일링하는 정적 요소.

| 클래스명 | 용도 | 사용 챕터 |
|---------|------|----------|
| `.plotly-demo` | Plotly iframe 래퍼 | 전체 |
| `.demo-label` | 데모 위 라벨 | 전체 |
| `.situation-card` | 상황카드 미리보기 (정적) | ch02, ch03 |
| `.analysis-step` | 분석 단계 카드 | ch04, ch05, ch06 |
| `.soul-doc-preview` | Soul Document 미리보기 | ch04, ch05 |
| `.verification-checklist` | 검증 체크리스트 | ch06 |
| `.peer-review-lens` | 피어리뷰 렌즈 카드 | ch07, ch08 |
| `.rubric-table` | 루브릭 테이블 스타일 | ch03, ch08, ch10 |
| `.ba-grid` | Before/After 비교 | ch04, index |
| `.hero-dashboard` | 히어로 대시보드 | index |
| `.journey-roadmap` | 학습 여정 로드맵 | index |
| `.cta-section` | Call-to-Action | index |
| `.part-header` | 파트 헤더 (색상별) | 전체 |
| `.timeline-flow` | 수업 시간 흐름 | ch02, ch04, ch07, ch09 |

---

## 6. Markdown 삽입 패턴

### 6.1 Plotly 차트 삽입

```markdown
<div class="plotly-demo" markdown>
<div class="demo-label">차트 제목</div>
<iframe src="../demos/파일명.html"></iframe>
</div>
```

### 6.2 Mermaid 다이어그램 삽입

````markdown
```mermaid
flowchart LR
    A[시작] --> B[과정] --> C[결과]
```
````

### 6.3 상황카드 정적 미리보기

```markdown
<div class="situation-card" data-category="school">
  <div class="card-icon">🏫</div>
  <div class="card-title">학교생활</div>
  <div class="card-text">"우리 학교 학생들이 잠을 못 자는 것 같아요"</div>
</div>
```

### 6.4 Before/After 비교

```markdown
<div class="ba-grid">
<div class="ba-before">
<div class="ba-label">❌ 나쁜 지시</div>
내용
</div>
<div class="ba-after">
<div class="ba-label">✅ 좋은 지시</div>
내용
</div>
</div>
```

### 6.5 분석 단계 카드

```markdown
<div class="analysis-step" data-step="1">
<div class="step-number">Step 1</div>
<div class="step-title">제목</div>
<div class="step-content">내용</div>
</div>
```

---

## 7. 데이터 사양

### 7.1 school_survey_200.csv

학교 생활 설문조사 가상 데이터 (200명).

| 컬럼명 | 한글명 | 데이터 유형 | 범위 |
|--------|--------|------------|------|
| id | 학번 | int | 1~200 |
| grade | 학년 | int | 1, 2, 3 |
| gender | 성별 | str | "남", "여" |
| smartphone_hours | 스마트폰 사용(시간/일) | float | 0.5~8.0 |
| sleep_hours | 수면시간(시간) | float | 4.0~10.0 |
| fatigue | 피로도 | int | 1~10 |
| satisfaction | 학교만족도 | int | 1~10 |
| exercise_days | 운동일수(주당) | int | 0~7 |
| study_hours | 공부시간(시간/일) | float | 0.5~6.0 |

**내장된 패턴**: 스마트폰↑ → 수면↓ (r≈-0.45), 운동↑ → 만족도↑ (r≈0.40)
**이상치**: 5~8개 (스마트폰 0.5h+수면 4h, 스마트폰 8h+수면 9h 등)

### 7.2 seoul_temperature.csv

서울 월별 평균기온 공공데이터 형식 (2015~2025).

| 컬럼명 | 한글명 | 데이터 유형 | 범위 |
|--------|--------|------------|------|
| year | 연도 | int | 2015~2025 |
| month | 월 | int | 1~12 |
| avg_temp | 평균기온(℃) | float | -5.0~28.0 |
| max_temp | 최고기온(℃) | float | 0.0~35.0 |
| min_temp | 최저기온(℃) | float | -12.0~23.0 |

**내장된 패턴**: 여름(7~8월) 평균기온 미세 상승 추세 (연 0.05~0.1℃)

---

## 8. 파트별 색상 체계

| Part | 이름 | 주색상 | HEX | 용도 |
|------|------|--------|-----|------|
| Part 1 | 질문을 설계하라 | 인디고 | #4361ee | 파트 헤더, 상황카드 테두리 |
| Part 2 | AI에게 분석을 시켜라 | 에메랄드 | #2ec4b6 | 분석 단계 카드, Soul Doc |
| Part 3 | 단짝 분석을 부숴라 | 앰버 | #f4a261 | 피어리뷰 카드 |
| Part 4 | 처음부터 끝까지 | 바이올렛 | #9b5de5 | 프로젝트 카드 |

---

## 9. 반응형 브레이크포인트

| 너비 | 변경 사항 |
|------|----------|
| > 768px | 기본 레이아웃 |
| ≤ 768px | 2열 → 1열, iframe 높이 축소, 카드 세로 배치 |
| ≤ 480px | 폰트 축소, 여백 축소, 데모 라벨 축소 |
