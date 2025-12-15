# 💊 F-DRA: Fitness Drug-use Response Analysis

## 온라인 피트니스 커뮤니티 내 '약물 사용(스테로이드)' 논쟁의
## 여론 구조 및 핵심 쟁점 분석 프로젝트

![Python](https://img.shields.io/badge/Python-3.8-blue)
![KoELECTRA](https://img.shields.io/badge/Model-KoELECTRA-green)
![BERTopic](https://img.shields.io/badge/Analysis-BERTopic-orange)
![Selenium](https://img.shields.io/badge/Crawler-Selenium-red)

---

## 1. 프로젝트 컨셉 및 문제의식

국내 피트니스 커뮤니티(디시인사이드, 에펨코리아 등)는 JavaScript 기반의 동적 페이지 구조를 사용하며, 익명성이 보장된 환경 특성상 비속어와 은어가 난무하는 비정형 텍스트가 주를 이룹니다. 따라서 본 프로젝트는 **Selenium 기반의 동적 크롤링**을 활용하여 실제 유저들의 날것 그대로의 논쟁 데이터를 수집하는 것부터 시작하였습니다.

기존의 온라인 여론 분석 프로젝트는 주로 단순 긍정/부정(Sentiment) 분류나 단순 빈도수 나열에 그쳤습니다. 그러나 이러한 접근은 복잡한 사회적 논쟁을 설명하는 데 한계가 있습니다. 예를 들어, 약물 사용에 대한 '반대'가 단순한 '혐오'인지, 아니면 '건강에 대한 우려'인지 구분하지 못하기 때문입니다.

본 프로젝트는 다음과 같은 문제의식에서 출발하였습니다.

> **단순한 비난과 옹호를 넘어, 익명 뒤에 숨겨진 대중의 진짜 논리(Logic)는 무엇인가?**
>
> **국내의 약물 논쟁은 해외(Reddit)와 비교했을 때, 어떤 특수성을 가지며 왜 생산적인 논의로 발전하지 못하는가?**

### 1.1. 💡 동적 페이지 크롤링 전략 (스크립트: `crawler.py`)

피트니스 커뮤니티는 게시글 리스트와 댓글이 동적으로 로딩되는 구조를 가지며, 특정 키워드 검색 시 다량의 노이즈(광고, 도배)가 섞여 있습니다. 본 프로젝트에서는 **Selenium과 Chrome Driver**를 활용하여 브라우저의 렌더링 과정을 제어하고, 실제 유저 행동을 모방하여 데이터를 수집하였습니다.

```python
# crawler.py 핵심 로직 예시
def get_community_data(driver, url):
    driver.get(url)
    
    # 사람의 행동 패턴을 모방한 랜덤 지연 (Anti-Crawling 회피)
    wait_time = random.uniform(2.0, 5.0)
    time.sleep(wait_time) 
    
    # 동적 로딩된 게시글 요소 추출
    posts = driver.find_elements(By.CLASS_NAME, 'gall_tit')
    # ... (생략)
