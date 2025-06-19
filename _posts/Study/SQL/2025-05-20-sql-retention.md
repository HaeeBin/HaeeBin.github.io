---
title: "리텐션"
excerpt: "리텐션"

categories:
  - SQL
tags:
  - [sql, 리텐션]

toc: true
toc_sticky: true

date: 2025-05-20
last_modified_at: 2025-05-20
---
# 리텐션(Retention)
## 1. 리텐션이란?
- 시간이 흐르면서 다시 제품(앱, 웹)을 사용하는지를 측정하는 지표
- 즉, "한 번 쓰고 끝나는 게 아니라 다시 찾아오는가?"를 보는 지표
- 시간이 흐르면서 계속 제품을 다시 사용하는 리텐션이 높은 서비스도 있고, 1회만 사용해서 재사용이 적은 리텐션이 낮은 서비스도 존재
  - 좋은 리텐션 = 사용자들이 제품을 꾸준히 찾는다는 뜻 → 제품 만족도, 충성도 높음
  - 나쁜 리텐션 = 써보긴 했지만 더 이상 쓰지 않음 → 문제 있는 경험 or 기대 미달
<br>

- PMF(Product Market Fit)을 찾았는가?를 생각할 때 리텐션 커브를 그려보고 판단
  - PMF : 제품이 고객의 핵심 문제 상황을 효과적으로 해결할 경우
  - 평평한 곳이 유지가 되면 PMF를 찾았다고 보곤 함

<br><br>

## 2. 리텐션 분석의 고민 포인트
- **Retain의 정의**가 필요함
- 주기에 따라 관점이 달라질 수 있음
  - 접속 주기가 긴 서비스가 존재. 이런 서비스들은 어떻게 해야 할까?
  - 하나의 제품에서 여러 페르소나가 있는 경우 팀마다 다른 주기로 리텐션을 볼 수도 있음
<br>

- 우리가 생각한 주기가 실제랑 같은지 파악
- Retention은 Output Metric => 원인을 파악하기 위해선 구체적으로 쪼개보는 작업이 필요

<br><br>

### 1) 우리 제품의 핵심과 연결하기 
- 우리 제품은 고객의 어떤 문제를 해결하려고 하는가?
- **핵심 이벤트**는 무엇인가?
  - 음식 주문 앱 -> 주문
  - 소셜 앱 -> 피드 보기
  - 택시 앱 -> 탑승
  - 이커머스 -> 구매

📌 핵심 이벤트 리텐션이야말로 진짜 제품 가치를 반영하는 리텐션임 <br>
-> 단순 '앱 접속'은 과정일 뿐 목표가 아님

<br><br>

### 2) 제품의 사용 주기 확인하기
- 얘상하는 사용 주기 떠올리기
  - 고객은 이 문제를 얼마나 겪는가? 어떤 빈도로 겪는가?
  - 예상한 주기와 실제 주기가 일치하는지 파악 : 잘못 생각하는 경우도 존재
- 최근 28일의 접속일 확인하기 : 간단하고 빠르게 파악할 수 있음
- 평균 사용 주기 확인하기
  - 2번 이상 사용한 사람들의 평균 사용 주기 구하기
  - 만약 유저의 첫 사용 주기의 간격을 알고 싶다면 첫 사용과 다음 사용의 간격을 계산

<br><br>

## 3. 리텐션의 종류

| 종류 | 정의 | 특징 | 적합한 경우 | 
| ------- | ---------- | ------------ | -----------|
| Classic (N day) | Day0 이후 DayN 별로 재참여 | 보수적, 수치 낮음 | 매일 사용하는 서비스, 단기 캠페인 측정 |
| Range (Bracket) | 특정 범위(N~M일)에 재참여 | 실용적, 유연함 | 주 단위 활동, 반복 이용 서비스 |
| Rolling (Unbounded) | DayN 이후 언제든 1번이라도 재참여 | 낙관적, 수치 높음 | 접속 주기 긴 서비스, 장기 사용자 관점 |

### 예시

| User | 가입일 | 1일 | 2일 | 7일 | 리텐션 종류별 참여 여부 |
| ---- | ----- | -- | -- | --- | ------------------ |
| A | 1/1 | O |  | O | Classic: O (Day1), <br> Range: O (1~7), Rolling: O |
| B | 1/1 | O | O |  | Classic: O (Day1), <br> Range: O, Rolling: O | 
| C | 1/1 |  |  | O | Classic: X (Day1), <br> Range: O, Rolling: O |

<br><br>

## 4. 리텐션 커브 해석
- 리텐션 커브의 목적: 아래로 가라앉는 게 당연하지만, 위로 올리고 평평하게 만드는 것이 핵심
- 평평한 구간이 생긴다면 → 이 시점 이후 유저는 "충성 유저"일 확률 높음

📌 리텐션은 단순 수치가 아니라 "면적(잔존 가치)" 관점에서 보는 것이 중요

<br><br>

# 리텐션을 SQL 쿼리로 작성하기

🎯 Day-N 리텐션 쿼리

```
-- Day 0: 첫 접속 날짜 추출
WITH first_visit AS (
  SELECT
    user_id,
    MIN(DATE(event_timestamp)) AS signup_date
  FROM events
  GROUP BY user_id
),

-- Day N 방문 확인 (N=1로 가정)
day_n_visit AS (
  SELECT
    e.user_id,
    DATE(e.event_timestamp) AS visit_date
  FROM events e
  JOIN first_visit f ON e.user_id = f.user_id
  WHERE DATE_DIFF(DATE(e.event_timestamp), f.signup_date, DAY) = 1
)

-- 리텐션 계산
SELECT
  f.signup_date,
  COUNT(DISTINCT f.user_id) AS day0_users,
  COUNT(DISTINCT d.user_id) AS day1_retained,
  ROUND(COUNT(DISTINCT d.user_id) / COUNT(DISTINCT f.user_id), 3) AS day1_retention_rate
FROM first_visit f
LEFT JOIN day_n_visit d ON f.user_id = d.user_id
GROUP BY f.signup_date
ORDER BY f.signup_date;
```

<br><br>

# 리텐션을 올리기 위해 무엇을 해야할까?
## 1. 리텐션 분석의 기본 마음가짐
- 코호트 등으로 나눠본 다음, 갑자기 수치가 좋은 경우 => 그걸 우리가 할 수 있을까?를 생각해봄
- 또는 특정 지역, 조건에 따라 다른 것을 찾는 과정
- 너무 예전 데이터 기반으로 찾을 경우 지금 당장의 Action Item을 찾기 어려울 수도 있음
(아이디어를 얻는 관점에선 괜찮을 수 있지만)

<br><br>

## 2. 리텐션을 높이기 위한 생각
- 제품 개선
  - 고객이 해결하고자 하는 문제를 어떻게 더 잘 해결할 수 있을까?
  - 문제를 해결하는 과정을 습관으로 만들 수 없을까?
- 온보딩 프로세스 개선
  - 고객이 처음 사용할 때 이 제품의 필요성을 더 느낄 수 있도록 할 수 없을까?
  - 제품의 가치를 느낀 시점은 언제일까?
- 주기적으로 제품을 사용해야 하는 이유가 있을까?
  - 그 이유를 까먹는다면 우리가 알려주는가?
- 고객이 이탈을 하려고 할 때 우리는 어떻게 하는가?
- 고객의 문제를 해결하고 있는 것을 응원하고 지지하고 있는가? 그걸 보여주었는가?