---
title: "Elasticsearch 개념을 알아보자!"
excerpt: "Lucene, Elasticsearch, data engineering, 검색엔진, 오픈소스

categories:
  - Data-Engineering
tags: 
  - [Elasticsearch, Lucene, 데이터엔지니어링]

toc: true
toc_sticky: true

date: 2025-07-11
last_modified_at: 2025-07-11
---

> 대용량의 데이터를 빠르게 검색하고 분석하려면 단순한 데이터베이스로는 부족하다.
> 이럴 때 사용하는 대표적인 검색 기술이 **Elasticsearch**다.  
>
> Elasticsearch는 로그, 메트릭, 검색 등 대용량 데이터를 다루는 프로젝트에서 **빠르고 유연한 검색 시스템**이 필요할 때 가장 많이 사용되는 도구다.

---

# 1. 검색엔진과 Lucene(루씬)
## 검색엔진의 기본 구조
- 검색엔진은 색인과정과 검색(질의)과정으로 나뉨

1️⃣ 색인(Indexing) 과정
- 검색 대상이 되는 데이터를 미리 분석하고 정리하는 단계
- 색인을 통해 문서의 단어들이 어떤 구조로 저장되는지 정리되어야, 나중에 빠른 검색이 가능

> 문서 수집 (파일, DB, 웹 등)
> 텍스트 분석 (형태소 분석, 불용어 제거 등)
>

## 🔍 Lucene(루씬)이란?
- 자바로 개발된 오픈 소스 고성능 정보 검색(Information Retrieval, IR) 라이브러리
  - IR : 문서를 검색하거나 문서의 내용을 검색하거나 문서와 연관된 메타 정보를 검색해 가는 과정
- 텍스트 데이터의 **색인(인덱싱)**과 검색에 특화되어 있음
- 루씬은 검색 기능을 갖춘 애플리케이션을 개발할 때 사용하는 소프트웨어 라이브러리
  - 루씬은 파일 검색, 웹 문서 수집, 웹 검색 등에 바로 사용할 수 있는 애플리케이션이 아님

<br>

## 루씬의 구조
- 루씬은 색인(Indexing)과 검색(Querying)과정이 있음
- 색인 단계에서는 문서를 Analyzer를 통해 토큰화하고, IndexWriter가 역색인을 생성해 저장
- 검색 단계에서는 사용자의 검색어를 QueryParser가 처리하고, Searcher가 색인된 데이터에서 관련 문서를 찾아 점수 기반으로 정렬된 결과를 반환

<br>

![Lucene 구성요소](/assets/images/posts_img/data_engineering_posts/lucene-구조.png)

✅ 색인(Indexing) 단계
- 문서를 읽고, 이를 색인하여 검색 가능한 형태로 저장하는 과정

<br>

1️⃣ File → Document
- Lucene에서 색인할 대상인 원본 파일을 읽어서, Lucene의 Document 객체로 변환
- 이 Document는 여러 필드(field)로 구성됨
  - 예를 들어 title, body, timestamp와 같은 속성을 가질 수 있음

<br>

2️⃣ Document → IndexWriter
- IndexWriter는 **색인 생성의 핵심 클래스**로, 문서를 받아 인덱스에 추가할 준비를 함

<br>

3️⃣ Document의 텍스트 → Analyzer
- IndexWriter는 Analyzer를 호출하여 텍스트를 토큰(단어)으로 나눔
- 예: "Elasticsearch is fast" → ["elasticsearch", "fast"]

<br>

4️⃣ IndexWriter → Index
- 분석된 토큰 정보를 Lucene의 역색인 구조로 변환하여 디스크에 저장

<br>

✅ 검색(Querying) 단계
- 색인이 완료된 후에는 사용자의 검색어에 따라 관련 문서를 찾아 반환함

<br>

5️⃣ Input → QueryParser
- 사용자가 검색창에 입력한 질의 문자열을 받아 **Lucene이 이해할 수 있는 쿼리 객체(Query)**로 변환

<br>

6️⃣ QueryParser → Analyzer
- 검색어도 문서와 동일하게 Analyzer를 거쳐 토큰화
- 문서 분석과 검색어 분석에 같은 분석기를 써야 정확한 비교가 가능함

<br>

7️⃣ IndexReader ← Index
- 검색을 위해 저장된 인덱스를 읽어오는 역할
- IndexReader는 색인된 문서를 검색 가능한 형태로 가져옴

<br>

8️⃣ IndexReader → Searcher
- Searcher는 Query와 IndexReader를 사용하여 관련 문서를 찾고, 점수를 계산하여 정렬함
- 검색된 결과는 관련도 점수에 따라 정렬됨 (TF-IDF, BM25 등 기반)
- 결과는 사용자에게 반환됨

<br>

# 🔍 1. Elasticsearch란?

- Apache Lucene(아피치 루씬) 기반의 Java 오픈소스 분산 검색 엔진
- 

