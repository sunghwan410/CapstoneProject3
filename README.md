# 💊 F-DRA: Fitness Drug-use Response Analysis
> **온라인 피트니스 커뮤니티 내 '약물 사용(스테로이드)' 논쟁의 여론 구조 및 핵심 쟁점 분석**

![Python](https://img.shields.io/badge/Python-3.8-blue)
![KoELECTRA](https://img.shields.io/badge/Model-KoELECTRA-green)
![BERTopic](https://img.shields.io/badge/Analysis-BERTopic-orange)
![Selenium](https://img.shields.io/badge/Crawler-Selenium-red)

---

## 1. 서론 (Introduction)

### 1.1 연구 배경 및 목적
최근 피트니스 시장의 급성장과 함께 스테로이드 등 불법 약물 사용(일명 '약투') 논란이 지속적으로 발생하고 있다. 하지만 이에 대한 논의는 주로 익명성이 보장된 온라인 커뮤니티에서 비정형 텍스트 형태로 산재해 있어, 여론의 실체와 핵심 쟁점을 파악하기 어렵다. 기존의 단순 긍정/부정(Sentiment) 분석으로는 복잡한 찬반 논쟁과 윤리적 쟁점을 설명하는 데 한계가 있다.

따라서 본 프로젝트는 **KoELECTRA** 기반의 문장 분류 모델과 **BERTopic**을 활용하여 다음을 규명한다.
1.  단순 호불호가 아닌 **5가지 입장(Stance)**으로 여론을 구조화한다.
2.  각 입장을 뒷받침하는 **핵심 논거(Argument)**를 데이터 기반으로 도출한다.
3.  국내 여론의 특수성을 해외 데이터와 비교 분석하여 시사점을 제공한다.

### 1.2 분석 대상 데이터
국내 대표 피트니스 커뮤니티인 **디시인사이드 헬스 갤러리**와 **에펨코리아 헬스 게시판**의 게시글 및 댓글 데이터를 분석 대상으로 한다.

---

## 2. 데이터 수집 (Data Collection)

### 2.1 수집 방법
피트니스 커뮤니티의 동적 페이지 특성(JavaScript 로딩)에 대응하기 위해 `Selenium`과 `Chrome Driver`를 활용하여 직접 크롤링을 수행했다. `random.sleep`을 적용하여 실제 사용자 행동을 모방함으로써 차단을 방지했다.

* **수집 도구:** Python Selenium, BeautifulSoup
* **수집 기간:** 202X.XX.XX ~ 202X.XX.XX
* **수집 키워드:** 약물, 스테로이드, 로이더, 내추럴, 약투, 도핑 등
* **총 데이터:** 게시글 및 댓글 총 **21,500건** 확보

### 2.2 크롤링 소스코드
크롤링 수행 코드는 본 리포지토리의 `src/crawler.py`에 포함되어 있다.
*(저작권 및 개인정보 보호를 위해 수집된 원본 텍스트 데이터 파일은 업로드하지 않음)*

```python
# 크롤링 코드 예시 (src/crawler.py 참조)
def get_comments(driver):
    comments = driver.find_elements(By.CLASS_NAME, 'comment_content')
    # ... (중략)
    return [c.text for c in comments]
