# 🧠 정부지원금 추천 챗봇 (RAG 기반)

[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/5BS4k7bR)

질문에 맞는 정부지원금 정보를 추천해주는 Retrieval-Augmented Generation (RAG) 기반 챗봇 시스템입니다.  
정부24 공공 서비스 데이터를 수집하고, Upstage의 Solar API와 LangChain을 활용하여 실시간으로 적절한 정보를 제공합니다.

- **프로젝트 기간:** 2025.03.01 ~ 2025.04.15  
- **주제:** Solar 기반 정부지원금 RAG 시스템 구축

---

# 🧑‍💻 팀원 소개

| 이름      | 역할                       | GitHub 링크               | 담당 기능                                                      |
|-----------|----------------------------|---------------------------|-----------------------------------------------------------------|
| 김지후     | RAG 시스템 전체 개발, 프롬프트 엔지니어링 | [@KJH121212](https://github.com/KJH121212) | Solar 임베딩 모듈 개발, 문서 청크 분할, 검색 모듈 구축 및 통합       |

---

# 🧩 파이프라인 워크플로우

```
User Query → 문장 임베딩 (Solar API) → 유사 문서 검색 (query_and_search.py) → 청크 필터링 → 답변 생성 (LLM Prompt)
```

---

# 📦 프로젝트 구조

```
C:.
│  .gitignore
│  README.md
│
├─code
│      lanhchain.ipynb
│
└─KJH
    │  app.py
    │  EDA.ipynb
    │  langchain+solar.ipynb
    │  main.py
    │
    ├─data_pipeline
    │      gov24_api_fetcher.py
    │      gov24_data_pipeline.py
    │      support_model_crawler.py
    │
    ├─docs
    │      data_pipeline_module_docs.md
    │      env_settings.md
    │      solar를 이용한 모델 파일 설명.md
    │      workflow.mmd
    │      임베딩_전처리_및_선택_기록.md
    │
    └─modules
            chunk_splitter.py
            data_loader.py
            llm_prompt.py
            query_and_search.py
            upstage_embedding.py
```

---

# ⚙️ 기술 스택 및 도구

- **LLM 및 프레임워크**: Upstage Solar API, LangChain
- **프로그래밍**: Python, Jupyter Notebook
- **데이터 전처리**: Pandas, JSON
- **검색 엔진**: FAISS 기반 유사도 검색 (Solar Embedding 사용)
- **기타**: .env 환경 변수 관리, Mermaid 다이어그램 문서화

---

# 🚀 핵심 구현 내용

## 🔍 문서 수집 및 전처리
- 정부24 공공 서비스 데이터 수집 및 통합 (지원 조건, 요약 등)
- JSON 병합 및 형식 정리 → 서비스 설명을 청크로 분할
- chunk_splitter.py를 통한 조건 기반 청크 구성

## 💡 검색 및 응답
- query_and_search.py에서 사용자 질문 임베딩 및 문서 검색
- 유사도가 높은 청크를 필터링하여 Solar API에 전달
- llm_prompt.py에서 질문 + 문서 + 조건을 조합한 응답 생성

## 🧪 실험 및 평가
- Solar 응답 품질 향상을 위한 프롬프트 템플릿 실험
- 검색 정확도와 응답 일치도 기준 평가 진행
- 유효한 정부서비스만 반환되도록 재확인 루틴 추가

---

# 💻 실행 방법

```bash
# 1. 환경 설정
cp .env.example .env  # Solar API 키 입력

# 2. 설치
pip install -r requirements.txt

# 3. 실행
python KJH/main.py
```

---

# 🌱 향후 계획

- ChatGPT Plugin 또는 Streamlit UI 적용
- 조건 기반 답변 하이라이트 기능 도입
- 더 많은 정부 API (복지로 등) 연동하여 확장성 확보

---

# 🙋 기타

본 프로젝트는 Upstage AI Lab 6기 NLP 트랙 실습의 일환으로 수행되었으며, 실제 정부지원 정보 탐색에 유용하게 활용할 수 있도록 설계되었습니다.
