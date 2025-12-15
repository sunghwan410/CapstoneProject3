# 💊 F-DRA: Fitness Drug-use Response Analysis
> **온라인 피트니스 커뮤니티 내 '약물 사용(스테로이드)' 논쟁의 여론 구조 및 핵심 쟁점 분석 프로젝트**

![Python](https://img.shields.io/badge/Python-3.8-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-1.12-orange)
![KoELECTRA](https://img.shields.io/badge/Model-KoELECTRA-green)
![Selenium](https://img.shields.io/badge/Crawler-Selenium-red)

---

## 📖 프로젝트 개요 (Overview)

### 1. 배경 및 문제의식
피트니스 시장의 급성장과 함께 스테로이드 등 불법 약물 사용(일명 '약투') 논란이 지속되고 있습니다. 관련 논의는 주로 익명성이 보장된 온라인 커뮤니티에서 비정형 텍스트로 이루어지며, 단순한 **긍정/부정(Sentiment)** 분석으로는 복잡한 찬반 논쟁과 윤리적 쟁점, 그리고 실질적인 위험 신호를 파악하기 어렵습니다.

### 2. 프로젝트 목표
본 프로젝트는 **KoELECTRA** 기반의 문장 분류 모델과 **BERTopic** 토픽 모델링을 결합하여 다음을 수행합니다.
1.  **Stance Classification:** 단순 호불호가 아닌 **5가지 입장(찬성, 반대, 정보 등)**으로 여론을 구조화.
2.  **Argument Mining:** 각 입장을 뒷받침하는 **핵심 논거(건강 vs 공정성)**를 데이터 기반으로 규명.
3.  **Insight:** 국내와 해외(Global)의 담론 차이를 분석하여 정책적 시사점 도출.

---

## 📊 데이터 수집 및 구축 (Data Pipeline)

### 1. Data Collection
* **Source:** 국내 대형 피트니스 커뮤니티 (디시인사이드 헬스갤러리, 에펨코리아)
* **Tool:** `Selenium` + `Chrome Driver` (동적 페이지 크롤링 및 차단 회피 적용)
* **Keywords:** 약물, 스테로이드, 로이더, 내추럴, 약투, 도핑 등
* **Volume:** 총 **21,500건** (게시글 및 댓글)

### 2. Preprocessing & Labeling
* **Cleaning:** HTML 태그, 이모지, 특수문자 제거.
* **Splitting:** `KSS (Korean Sentence Splitter)`를 활용한 문장 단위 분리.
* **Labeling:** 무작위 추출 문장 **2,000건**에 대해 5-Class 직접 라벨링 수행.

| Label | Description |
|:---:|:---|
| **Anti (반대/비판)** | 건강 우려, 공정성 위배, 사회적 파장 비판 |
| **Pro (찬성/옹호)** | 개인의 선택 존중, 효율성 강조, 표현의 자유 |
| **Info (정보/질문)** | 사용법 질문, 부작용 대처(PCT), 스택 공유 |
| **Neutral (중립)** | 단순 사실 전달, 판단 유보 |
| **Others (기타)** | 주제 무관 잡담, 광고, 욕설 |

---

## 🛠 분석 방법론 (Methodology)

### 1. Stance Classification Model
* **Base Model:** `monologg/koelectra-small-v3-discriminator`
    * 한국어 구어체 처리에 강점이 있는 ELECTRA 모델 채택.
* **Training:**
    * Input: 커뮤니티 문장 (Sentence)
    * Output: 5-Class Probability
    * Optimization: Class Weight를 적용하여 데이터 불균형(Imbalance) 해소.

### 2. Topic Modeling
* **Tool:** `BERTopic`
* **Objective:** 분류된 각 Stance 그룹(예: 반대 그룹) 내에서 의미론적 군집(Semantic Cluster)을 형성하여 핵심 키워드 추출.

---

## 📈 분석 결과 (Experimental Results)

### 1. 모델 성능 평가
* **Validation Accuracy:** **84.2%**
* **Macro F1-Score:** **0.81**
* **Result:** 데이터 불균형 상황에서도 소수 클래스인 '정보(Info)'와 '찬성(Pro)'을 유의미하게 구분해냄. 특히 문맥 파악이 중요한 **'비난'과 '정보 공유'의 차이를 명확히 학습함.**

![Model Confusion Matrix]([이미지 경로를_여기에_넣으세요])

### 2. 여론 구조 분석
* **지배적 여론:** 전체 데이터의 **46.0%**가 '반대/비판' 입장임.
* **잠재적 수요:** 단순 옹호(8.0%)보다 실질적인 사용법을 묻거나 정보를 공유하는 **'정보/질문'(15.0%)** 비중이 약 2배 높음.

![Stance Distribution]([이미지 경로를_여기에_넣으세요])

### 3. 심층 토픽 분석 (Why Anti?)
'반대' 입장의 논거는 단일하지 않고, 두 가지 핵심 축으로 이원화됨을 규명했습니다.
1.  **건강 우려 (Health Risk):** 심정지, 신장 투석, 고자, 수명 단축, 불임
2.  **공정성/윤리 (Fairness):** 내추럴 코스프레, 사기꾼, 거짓말, 시합 조작

---

## 🌍 국내 vs 해외 비교 (Comparative Analysis)

국내(Community)와 해외(Reddit) 데이터의 토픽 비중을 비교 분석하였습니다.

* **국내 (Korea): Moral Verification**
    * 약물 사용의 진위 여부(내추럴인가 아닌가)와 **도덕성 비난**에 논의가 집중됨.
* **해외 (Global): Harm Reduction**
    * 비난보다는 **부작용 최소화(Harm Reduction)**, 혈액 검사(Blood Work), 케어(PCT) 등 **실용적 정보 공유**가 주류를 이룸.

---

## 🚀 결론 및 기대효과

본 프로젝트는 텍스트 마이닝을 통해 익명 커뮤니티의 여론을 정량적 데이터로 구조화했습니다.
분석 결과, 국내 여론의 핵심 트리거는 약물 사용 자체가 아닌 **'기만 행위(거짓말)'**에 있음을 밝혔습니다. 본 모델은 향후 **약물 오남용 위험군 자동 탐지** 및 **올바른 정보 양지화 정책 수립**의 기초 자료로 활용될 수 있습니다.

---

## 💻 설치 및 실행 (Usage)

```bash
# Repository Clone
git clone [https://github.com/YourUsername/F-DRA.git](https://github.com/YourUsername/F-DRA.git)

# Install Requirements
pip install -r requirements.txt

# Run Crawler (Chrome Driver Required)
python src/crawler.py

# Run Model Training
python src/train.py

# Run Analysis & Visualization
python src/analysis.py
