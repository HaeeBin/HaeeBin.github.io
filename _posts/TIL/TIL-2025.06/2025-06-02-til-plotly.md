---
title: "TIL - 2025.06.02"
excerpt: "TIL, Plotly, Python"

categories:
  - til-2025
tags:
  - TIL
  - Python
  - Plotly

toc: true
toc_sticky: true

date: 2025-06-02
last_modified_at: 2025-06-02
---
# 2025-06-02 · Day 19

> **일독일행 — 하루에 한 가지는 반드시 가져간다**
> 
> _“오늘의 하나:_ **plotly.express**”

---

## Abstract · 오늘 배운 것 한눈에 보기 (1‑4줄)
- Plotly는 웹 기반의 인터랙티브한 시각화를 지원하는 Python 라이브러리로, `plotly.express`를 사용하면 간결한 코드로 다양한 그래프를 생성할 수 있다.
- 이번 실습에서는 산점도, 선 그래프, 막대 그래프, 파이 차트 등 기본 차트를 다뤘고, `hovertemplate`, `animation_frame`, `facet_row` 등 고급 기능도 실습했다.
- 또한 `plotly.graph_objects`와 `make_subplots`를 활용해 복합 레이아웃과 3D 시각화 구성 방법도 다뤘다.
  
---

## Keywords Hierarchy

| 익숙한 개념 ✅   | 조금 아는 개념 🔍 | 잘 모르는 개념 ❓ | 전혀 새로운 개념 🚀 |
| --------------- | --------------- | -------------- | ------------ |
| pandas | subplot | plotly.express, hovertemplate | volume rendering, 3D 시각화 |

> **Tip :** 각 열에 최대 3개만 적어 집중도를 유지하세요.

---

### Deep‑Dive Notes (선택한 키워드 정리)

#### 1. 익숙한 개념

- pandas
  - **언제 사용?**
    - csv, Excel 등 파일에서 표 데이터를 불러오거나 데이터 처리와 같은 작업을 수행을 하기 위함.
    - Series와 DataFrame을 통해 데이터 가공, 집계, 필터링 등을 효율적으로 처리할 수 있음.
    - Plotly에서 그래프를 그리기 위해서는 Dataframe을 기본으로 사용하기 때문에 pandas 사용
  - **💡핵심 포인트**
    - `groupby()`, `query()`, `value_counts()`, `reset_index()` 자주 활용
    - 시각화 전에 전처리를 깔끔하게 해야 시각화가 잘 나옴
  - **🧪예시**
    
    ```python
    avg_tip_by_day = tips_df.groupby('day', as_index=False)['tip'].mean()
    ```
    <br><br>
    
#### 2. 조금 아는 개념

- subplot
  - **개념 요약**
    - 여러 개의 그래프를 하나의 화면에 배치하는 기능
    - `make_subplots`함수를 통해 그리드(행과 열) 형태의 레이아웃을 만들고 각 셀에 다른 종류의 그래프를 추가할 수 있다.
  - **💡핵심 포인트**
    - `rows`, `cols`, `subplot_titles`, `specs` 인자를 활용
    - `fig.add_trace(trace, row=r, col=c)`와 같은 방식으로 Figure 객체에 trace 추가하면서 위치 지정한다.
    - 레이아웃이나 축 제목은 `update_layout()`, `update_xaxes()` 등으로 제어한다.
  - **🧪예시**
    
    ```python
    fig = make_subplots(rows=1, cols=2, subplot_titles=["히스토그램", "박스플롯"])
    fig.add_trace(go.Histogram(x=tips_df['total_bill']), row=1, col=1)
    fig.add_trace(go.Box(y=tips_df['tip'], x=tips_df['day']), row=1, col=2)
    ```
    <br><br>

#### 3. 잘 모르는 개념

- plotly.express
  - **개념 요약**
    - Plotly의 고수준 시각화 API로, 간결한 문법으로 다양한 인터랙티브 그래프를 생성할 수 있다.
    - `px.scatter`, `px.line`, `px.bar`, `px.pie`, `px.violin`, `px.histogram`, `px.sunburst` 등을 통해 다양한 그래프들을 생성할 수 있다. 
  - **💡핵심 포인트**
    - `facet_row`, `animation_frame`, `hover_data`, `color_discrete_sequence` 등 옵션이 강력하다.
    - 코드가 간결하면서도 시각적으로 뛰어난 결과를 제공한다.
  - **🧪예시**
    
    ```python
    fig = px.scatter(gapminder_2007, x="gdpPercap", y="lifeExp",
                 size="pop", color="continent",
                 hover_name="country", log_x=True)
    ```
    <br><br>

- hovertemplate
  - **개념 요약**
    - 데이터 포인트에 마우스를 올렸을 때 표시되는 툴팁(hover info)을 완전히 커스터마이징할 수 있는 문법이다. 
    - HTML 유사 템플릿을 사용한다. 
  - **💡핵심 포인트**
    - `fig.update_traces(hovertemplate="...")`로 설정한다.
    - `{x}`, `{y}`,`{customdata[0]}` 등 변수를 사용한다.
    - `<extra></extra>`로 trace명 제거 가능하다. 
  - **🧪예시**
    
    ```python
    fig.update_traces(
        hovertemplate="<b>%{hovertext}</b><br>GDP: %{x:,.0f}<br>Life Exp: %{y:.1f}년"
    )
    ```
    <br><br>    

#### 4. 전혀 새로운 개념

- volume rendering
  - **개념 요약**
    - 3차원 스칼라 필드(예: 의료 영상 데이터 MRI, CT 스캔)의 내부 구조를 시각화한다.
    - 밀도에 따라 다른 색상과 투명도를 부여하여 표현한다.
  - **💡핵심 포인트**
    - `value`, `x/y/z`, `opacity`, `isomin`, `isomax` 조정
    - 3D 내부 밀도를 색상과 투명도로 표현
  - **🧪예시**
    
    ```python
    fig = go.Figure(go.Volume(
        x=X.flatten(), y=Y.flatten(), z=Z.flatten(),
        value=V.flatten(), opacity=0.1, surface_count=15
    ))
    ```
    <br><br>    

- 3D 시각화
  - **개념 요약**
    - 데이터의 세 가지 변수(x, y, z)를 시각화하기 위한 방법
    - 점 위치, 크기, 색상을 통해 4차원 이상 표현도 가능하다.
  - **💡핵심 포인트**
    - `scene` 옵션을 통해 축 제목 및 범위 설정
    - mesh 또는 surface 형태로 복잡한 구조 표현 가능
    - 시각적으로 드래그와 회전을 통한 데이터 탐색 가능
  - **🧪예시**
    
    ```python
    fig = px.scatter_3d(iris_df, x='sepal_length', y='sepal_width', z='petal_width',
                    color='species', size='petal_length')
    ```
    <br><br>        

---

## Reflection · 느낀 점 & 도전 과제 

- 파이썬의 시각화 라이브러리 중 Plotly를 배웠는데, 짧은 코드로 다양한 그래프가 표현된다는 것에 놀랐다.
- 간단한 그래프는 실습으로 이해가 되고 괜찮지만, 복잡한 그래프의 경우 좀 이해가 되지 않아서 학습이 더 필요하다고 느꼈다. 
- 작동 방식 등에 대한 이해가 끝나면 그래프 생성에 대한 연습을 해야할 것 같다.

---

## Tomorrow’s Action — _“One Small Step”_

- 실습 파일에 있는 연습문제 다시 풀어보기
- 공공데이터 활용해서 시각화 연습해보기

---

## Resources & Links

- [Plotly 공식 문서](https://plotly.com/python/getting-started/)
    
- [Plotly Tutorial - 파이썬 시각화의 끝판왕 마스터하기](https://wikidocs.net/book/8909)
    

---

※ 이 로그는 나만의 발자취를 남기기 위한 개인 학습 기록입니다. 공유 목적이 아닌 **성장 추적**에 집중하세요.

_**© 2025 회고 없는 성장은 없다**_
