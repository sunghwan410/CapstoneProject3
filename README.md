# 💊 F-DRA: Fitness Drug-use Response Analysis

> **온라인 피트니스 커뮤니티 내 '약물 사용(스테로이드)' 논쟁의 여론 구조 및 핵심 쟁점 분석**

![Python](https://img.shields.io/badge/Python-3.8-blue)
![KoELECTRA](https://img.shields.io/badge/Model-KoELECTRA-green)
![BERTopic](https://img.shields.io/badge/Analysis-BERTopic-orange)
![Selenium](https://img.shields.io/badge/Crawler-Selenium-red)

---

## 1. 서론 (Introduction)

### 1.1. 연구 배경 및 목적
최근 피트니스 시장의 급성장과 함께 스테로이드 등 불법 약물 사용(일명 '약투') 논란이 지속되고 있습니다. 하지만 관련 논의는 주로 익명성이 보장된 온라인 커뮤니티(디시인사이드, 에펨코리아 등)에서 비정형 텍스트로 이루어져, 여론의 실체와 핵심 쟁점을 파악하기 어렵습니다. 

기존의 단순 긍정/부정(Sentiment) 분석으로는 복잡한 찬반 논쟁과 윤리적 쟁점을 설명하는 데 한계가 있습니다. 따라서 본 프로젝트는 **KoELECTRA** 기반의 문장 분류 모델과 **BERTopic**을 활용하여 다음을 규명합니다.

1.  단순 호불호가 아닌 **5가지 입장(Stance)**으로 여론을 구조화
2.  각 입장을 뒷받침하는 **핵심 논거(Argument)**를 데이터 기반으로 도출
3.  국내 여론의 특수성을 해외 데이터와 비교 분석하여 정책적 시사점 제공

### 1.2. 분석 대상 데이터
국내 대표 피트니스 커뮤니티인 **디시인사이드 헬스 갤러리**와 **에펨코리아 헬스 게시판**의 게시글 및 댓글 데이터를 분석 대상으로 합니다.

---

## 2. 데이터 수집 (Data Collection)

### 2.1. 수집 방법 (Dynamic Crawling)
피트니스 커뮤니티의 동적 페이지 특성(JavaScript 로딩)에 대응하기 위해 `Selenium`과 `Chrome Driver`를 활용하여 직접 크롤링을 수행했습니다. `random.sleep`을 적용하여 실제 사용자 행동을 모방함으로써 차단을 방지했습니다.

* **수집 도구:** Python Selenium, BeautifulSoup
* **수집 키워드:** 약물, 스테로이드, 로이더, 내추럴, 약투, 도핑 등
* **총 데이터:** 게시글 및 댓글 총 **21,500건** 확보

### 2.2. 크롤링 소스코드 (Core Logic)
크롤링 수행 코드는 본 리포지토리의 `src/crawler.py`에 포함되어 있습니다.

```python
from selenium import webdriver
import time
import random

def get_community_data(url):
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')
    driver = webdriver.Chrome(options=options)
    
    driver.get(url)
    time.sleep(random.uniform(2, 4)) # 차단 방지
    
    # 동적 로딩된 게시글 요소 추출 로직
    # ...

## 3. 여론 구조 분석 및 데이터 해석

학습된 모델을 통해 전체 데이터를 분류하고, 그 분포를 분석하였습니다.

### 3.1. 전체 입장 분포 (Stance Distribution)

https://github.com/sunghwan410/CapstoneProject3/blob/main/%EB%84%A4%EB%AA%A8%EA%B7%B8%EB%9E%98%ED%94%841.png?raw=true


* **반대/비판 (46.0%):**
    여론의 절반 가까이가 약물 사용에 대해 부정적입니다. 이는 커뮤니티가 약물 사용을 강력하게 배척하는 규범을 가지고 있음을 시사합니다.

* **정보/질문 (15.0%):**
    단순 옹호(8.0%)보다 실질적인 정보를 묻거나 공유하는 비율이 약 2배 높습니다. 이는 **잠재적 사용자 층**이나 **부작용 위험군**이 상당히 존재함을 의미합니다.

---

## 4. 심층 토픽 분석 (BERTopic)

단순히 "반대가 많다"는 사실을 넘어, **"왜 반대하는가?"**를 규명하기 위해 분류된 각 입장 그룹에 대해 **BERTopic (토픽 모델링)**을 수행하였습니다.

### 4.1. 의미론적 군집 시각화 (Semantic Clusters)

![Semantic Clusters](점그래프1.png)
<img width="551" height="428" alt="점그래프1" src="https://github.com/user-attachments/assets/fe9e85ba-6542-450b-9852-a643bf00e65e" />

위 시각화는 문장들의 의미론적 거리를 2차원으로 매핑한 결과입니다.

* **반대(Red) 논거의 이원화:**
    반대 여론은 단일하지 않고 두 가지 축으로 뚜렷하게 나뉩니다.
    1.  **건강 우려 (Health Risk):** '심정지', '신장 투석', '고자', '수명' 등 사용자를 **'환자'**로 보고 걱정하는 시선.
    2.  **공정성/윤리 (Fairness):** '내추럴 코스프레', '사기꾼', '거짓말' 등 대중을 속이는 **'기만행위'**에 대한 분노.

* **정보(Green) 공유의 특성:**
    감정이 배제된 **'실용적 데이터(PCT, 용량, 검사)'** 위주의 독자적인 군집을 형성합니다.

---

## 5. 국가별 담론 특성 비교 (Comparative Analysis)

국내 여론의 특수성을 파악하기 위해, 해외 최대 커뮤니티인 **Reddit(r/steroids)** 데이터와 토픽 비중을 비교 분석하였습니다.

### 5.1. 국내 vs 해외 주요 토픽 비중 비교


| 구분 | 주요 토픽 (Dominant Topic) | 해석 |
| :--- | :--- | :--- |
| **국내 (Korea)** | **도덕성 비난 (Moral Criticism)** | 약물 사용 여부(Natural or Not)의 **진위 판별**과 **거짓말**에 대한 비난이 압도적입니다. 피트니스를 '공정한 경쟁'의 장으로 인식하는 경향이 강합니다. |
| **해외 (Global)** | **유해성 감소 (Harm Reduction)** | 비난보다는 **'어떻게 하면 부작용을 줄일 것인가'**에 대한 실용적 정보 공유(Blood Work, PCT)가 주류를 이룹니다. |

---

## 6. 모델 설계 및 학습 성능

본 프로젝트는 한국어 구어체의 미묘한 뉘앙스를 포착하기 위해 **KoELECTRA-small** 모델을 기반으로 Fine-tuning을 진행하였습니다.

### 모델 구성

* **Model:** `monologg/koelectra-small-v3-discriminator`
* **Task:** Multi-class Text Classification (5 Classes)
* **Input:** 커뮤니티 문장 (Sentence)
* **Loss:** Cross Entropy Loss (with Class Weights)

 모델의 분류 정확도가 높음을 보여줍니다.
    * 특히 문맥 파악이 중요한 **'정보(Info)'와 '비난(Anti)'을 명확히 구분(상호 오분류 적음)**해내는 성과를 거두었습니다.
