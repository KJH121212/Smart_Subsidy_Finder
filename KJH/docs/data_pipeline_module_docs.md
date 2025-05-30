## 📄 주요 파일 설명

### `gov24_data_pipeline.py`
- 전체 데이터 수집 및 가공 파이프라인의 **메인 실행 파일**
- ✅ 주요 역할:
  - 정부24 OpenAPI 호출
  - 지원조건 항목 코드 크롤링
  - 항목 설명 매핑 및 중복 컬럼 통합
  - 조건 및 연령 필드 가공
  - 서비스명 기준 병합 처리
  - `.json` 형태로 저장
  - 저장 전 기존 `.json` 파일은 `_prev.json` 형식으로 백업
- ✅ 특징:
  - **해당 파일 하나만 실행하면 전체 파이프라인이 자동 수행됨**
  - 항목코드 중복 설명을 자동 처리하며 조건 딕셔너리를 구성함
  - 서비스명 기준 중복 데이터를 통합하여 최종 병합본 생성

---

### `gov24_api_fetcher.py`
- 정부24 OpenAPI로부터 데이터를 수집하는 함수가 정의된 모듈
- ✅ 주요 기능:
  - 인코딩된 API 키와 URL을 받아 **페이지네이션 처리** 및 **재시도 로직 포함**
  - 전체 데이터를 `pandas.DataFrame` 형태로 반환
- 📌 사용 API 예시:
  - `https://api.odcloud.kr/api/gov24/v3/serviceDetail`
  - `https://api.odcloud.kr/api/gov24/v3/supportConditions`

---

### `support_model_crawler.py`
- [공공데이터포털 OpenAPI 문서 페이지](https://www.data.go.kr/data/15113968/openapi.do#/)에서  
  지원조건 항목 코드 및 설명을 크롤링하는 모듈
- ✅ 주요 기능:
  - 셀레니움을 이용해 `supportConditions_model` 테이블을 크롤링
  - 항목코드-설명 매핑 정보를 수집해 `pandas.DataFrame`으로 반환
  - 필요 시 `.csv` 또는 `.json` 저장 가능

---

### `EDA.ipynb`
- `combined_service_data.json`의 내용을 확인하거나 수정을 고민할 때 사용하는 **보조 분석 파일**
- ✅ 주요 기능:
  - 병합 데이터의 구조 확인
  - 특정 조건 필터링, 정제 필요 여부 검토
  - 향후 데이터 기반 탐색(EDA) 및 임베딩을 위한 점검

---

## 📁 `data/` 디렉토리 파일 설명

정부24 공공서비스 데이터를 수집·정제한 결과물이 저장되는 폴더.  
모든 파일은 `.json` 형식이며, 백업 파일은 `_prev.json` 접미사를 가짐.

---

### `serviceDetail_all.json`
- ✅ 설명:  
  - 정부24 API의 `serviceDetail` 엔드포인트에서 수집한 서비스 상세 정보
  - 서비스명, 신청방법, 지원내용 등 **기본 텍스트 정보** 포함
- 📌 비고:  
  - 원시 상태의 상세 서비스 데이터

---

### `supportConditions_all.json`
- ✅ 설명:  
  - 정부24 API의 `supportConditions` 엔드포인트에서 수집한 **지원조건 정보**
  - 조건 필드는 Y/N 형식으로 되어 있음 (예: 다문화가족, 저소득층 등)

---

### `supportConditions_model.json`
- ✅ 설명:  
  - `supportConditions_all`의 컬럼명이 의미를 알 수 없는 코드(`D00`, `T02` 등)로 되어 있기 때문에  
    해당 코드를 사람이 읽을 수 있는 설명으로 **매핑**한 정보
  - 셀레니움을 이용해 OpenAPI 문서에서 직접 크롤링

---

### `combined_service_data.json`
- ✅ 설명:  
  - 위 세 데이터를 기반으로 병합 및 가공된 **최종 서비스 정보**
  - 구조:
    - `서비스ID`, `서비스명`, `지원내용`, `지원대상`, `신청방법` 등
    - `조건`: boolean 필드들의 딕셔너리
    - `대상연령`: 시작/종료 나이 포함
- 📌 비고:  
  - RAG 시스템이나 임베딩에 사용될 수 있는 **통합 데이터셋**

---

### `combined_service_data_merged.json`
- ✅ 설명:
  - `combined_service_data.json`을 **서비스명 기준으로 중복 병합 처리한 최종 데이터**
  - 텍스트 필드(예: 지원내용)는 중복 시 `||` 구분자로 합쳐지고, 딕셔너리 및 리스트 필드는 병합됨
- 📌 비고:
  - 실질적인 서비스별 통합 정보를 제공하는 데이터
  - 모델 임베딩이나 검색 시스템 구축에 직접 활용 가능

---

### `*_prev.json` (백업 파일들)
- ✅ 설명:  
  - 해당 원본 `.json` 파일이 업데이트되기 전 자동으로 백업된 버전
  - 예: `combined_service_data_prev.json`, `supportConditions_all_prev.json` 등
- 📌 비고:  
  - 변경 여부 추적, 임베딩 비교 등 용도로 활용 가능
  - 항상 최신 직전 버전만 유지됨 (이전 버전 덮어씀)

---

### ✅ 예시 흐름

| 파일명                          | 생성 시점                         | 내용 구성                        |
|----------------------------------|------------------------------------|----------------------------------|
| `serviceDetail_all.json`         | API 호출 직후                      | 상세 텍스트 정보                 |
| `supportConditions_all.json`     | API 호출 직후                      | 조건 코드(Y/N)                  |
| `supportConditions_model.json`   | 셀레니움 크롤링 직후              | 항목코드-설명 매핑 테이블       |
| `combined_service_data.json`     | 모든 데이터 병합 후 저장           | RAG용 통합 문서 데이터           |
| `combined_service_data_merged.json`| 서비스명 기준 병합된 최종 데이터 | 최종 RAG 입력용 병합 데이터     |
| `*_prev.json`                    | 저장 전 자동 백업                  | 변경 이력 추적용                |
