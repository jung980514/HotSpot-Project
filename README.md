# HotSpot
# 부산대학교 정보컴퓨터공학부 2022 전기 졸업과제

* 주제 : 관광 hotspot 빅데이터 분석 웹서비스<br>


![Python](https://img.shields.io/badge/Python-3.8%2B-blue?logo=python)
![Django](https://img.shields.io/badge/Django-4.0.5-darkgreen?logo=django)
![MySQL](https://img.shields.io/badge/MySQL-8.0-blue?logo=mysql)
![Selenium](https://img.shields.io/badge/Selenium-4.4-green?logo=selenium)
![Bootstrap](https://img.shields.io/badge/Bootstrap-5.x-purple?logo=bootstrap)

<br>

> 대한민국 주요 관광지의 인기도를 분석하고 사용자 맞춤형 관광 정보를 제공하는 웹 서비스

`HotSpot Project`는 데이터 크롤링을 통해 수집한 관광지 정보를 분석 및 시각화하고, 사용자에게 맞춤형 경로 추천과 상세 정보를 제공하는 Django 기반 웹 애플리케이션입니다.

---

##  목차

1.  [**✨ 주요 기능**](#-주요-기능)
2.  [**⚙️ 시스템 아키텍처**](#️-시스템-아키텍처)
3.  [**🛠️ 기술 스택**](#️-기술-스택)
4.  [**💾 데이터베이스 명세**](#-데이터베이스-명세)
5.  [**🌐 API 엔드포인트 명세**](#-api-엔드포인트-명세)
6.  [**📊 데이터 파이프라인**](#-데이터-파이프라인)
7.  [**🚀 시작하기**](#-시작하기)
8.  [**📂 프로젝트 구조**](#-프로젝트-구조)

---

## ✨ 주요 기능

-   **인기 관광지 대시보드**: 지역별(부산) 인기 관광지 순위를 테이블과 차트로 시각화하여 제공합니다.
-   **관광지/축제 정보**: 진행 중인 축제 정보와 관광지 상세 정보를 제공하며, 댓글로 소통할 수 있습니다.
-   **지도 기반 검색 및 경로 추천**: Kakao Maps API를 연동하여 지도 위에서 관광지를 검색하고, 여러 장소를 경유하는 최적 경로를 추천합니다.
-   **데이터 기반 순위 제공**: 크롤링한 데이터를 가중치에 따라 계산하여 자체적인 인기 순위를 제공합니다.

## ⚙️ 시스템 아키텍처

프로젝트의 데이터 흐름과 구성 요소는 다음과 같습니다.

```
[Web Scrapers] ----> [Raw Data] ----> [Data Processor] ----> [MySQL DB] ----> [Django Backend] <----> [Frontend]
  (Selenium,           (.csv)         (Jupyter, Pandas)                      (Django REST)         (HTML, JS,
  BeautifulSoup)                                                                                    Kakao Maps)
```

1.  **데이터 수집 (Crawling)**: `Selenium`과 `BeautifulSoup`을 이용해 공공데이터 및 포털 사이트에서 관광지, 축제, 리뷰 데이터를 수집합니다.
2.  **데이터 처리 (Processing)**: `Pandas`와 `Jupyter Notebook`을 활용해 수집된 데이터를 정제하고 분석하여 유의미한 정보를 추출합니다.
3.  **데이터베이스 저장 (Storage)**: 처리된 데이터를 `MySQL` 데이터베이스에 저장합니다.
4.  **백엔드 API (Backend)**: `Django`와 `Django REST Framework`를 사용해 데이터베이스의 정보를 조회하고, 비즈니스 로직을 처리하는 API를 제공합니다.
5.  **프론트엔드 (Frontend)**: `HTML`, `Bootstrap`, `JavaScript`로 구성된 사용자 인터페이스를 통해 정보를 시각화하고, `Kakao Maps API`를 통해 지도 서비스를 제공합니다.

## 🛠️ 기술 스택

| 구분       | 기술                                       |
| :--------- | :----------------------------------------- |
| **Backend**| `Python`, `Django`, `Django REST Framework`|
| **Frontend**| `HTML`, `CSS`, `JavaScript`, `Bootstrap`   |
| **Database**| `MySQL`                                    |
| **Crawling**| `Selenium`, `BeautifulSoup4`, `requests`   |
| **Data**    | `Pandas`, `NumPy`, `Jupyter Notebook`      |
| **API**     | `Kakao Maps API`, `Kakao Mobility API`     |

---

## 💾 데이터베이스 명세

<details>
<summary>자세히 보기</summary>

| 테이블명 (`Model`) | 설명 | 주요 컬럼 |
| :--- | :--- | :--- |
| **`Hotspot`** | 인기 관광지 정보 | `name`, `address`, `category`, `search` |
| **`Festival`** | 축제 기본 정보 | `festival_name`, `latitude`, `longitude`, `start_date`, `end_date` |
| **`FestivalInfo`** | 축제 상세 정보 | `host`, `phone_number`, `address`, `detail_info` |
| **`FestivalImg`** | 축제 이미지 URL | `image_url` |
| **`CommentFestival`**| 축제 댓글 | `username`, `content`, `created_at` |
| **`Place`** | 부산 내 장소 정보 | `name`, `category`, `address`, `operation_time`, `avg_rate` |
| **`Trend`** | 일별 검색 트렌드 | `keyword`, `area_name`, `mobile_search`, `pc_search`, `search_date` |
| **`TopPlace` / `TopFood`** | 자체 산정 인기 장소/맛집 | `place`, `score` |

</details>

## 🌐 API 엔드포인트 명세

<details>
<summary>자세히 보기</summary>

| Method | URL                                       | View 함수 (`views.py`) | 설명 |
| :--- | :---------------------------------------- | :--- | :--- |
| `GET` | `/`                                       | `showTable` | 메인 페이지. 부산 지역별 인기 장소와 축제 목록을 보여줍니다. |
| `GET` | `/dashboard`                              | `dashboard` | 대시보드 페이지. 관광지 목록을 테이블 형태로 제공합니다. |
| `GET` | `/festival`                               | `show_festival` | 진행 중인 축제 목록을 페이지네이션하여 보여줍니다. |
| `GET` | `/festival/<int:festival_id>/`            | `festival_detail` | 특정 축제의 상세 정보, 이미지, 댓글을 보여줍니다. |
| `POST`| `/festival/write_comment/<int:festival_id>`| `create_comment_festival` | 특정 축제에 댓글을 작성합니다. |
| `POST`| `/festival/delete_comment/...`            | `delete_comment_festival` | 특정 축제의 댓글을 삭제합니다. |
| `GET` | `/busan/`                                 | `busan` | 카카오맵 기반의 부산 관광 페이지를 렌더링합니다. |
| `GET` | `/busan/getPlaceList/`                    | `get_place_list` | 부산 내 장소를 검색하고 정렬하여 목록을 반환합니다. (AJAX) |
| `GET` | `/api/getRoute/`                          | `get_route` | 출발지, 경유지, 목적지를 받아 최적 경로를 반환합니다. (Kakao Mobility API) |

</details>

## 📊 데이터 파이프라인

-   **Crawling Scripts (`/category/crawling/`)**: `main.py`를 중심으로 `populartourspot`의 원본 데이터를 가공하거나, `kakaomap_selenium.py`를 통해 직접 웹에서 정보를 수집합니다.
-   **Jupyter Notebooks (`/*.ipynb`)**: 수집된 데이터를 탐색하고 분석 모델(`model.ipynb`, `recommend.ipynb`)을 실험하는 데 사용됩니다.
-   **Data Insertion (`/category/util/`)**: `insertData.py`와 같은 유틸리티 스크립트를 사용해 정제된 CSV 파일을 DB에 일괄 삽입합니다.

---

## 🚀 시작하기

### 1. 사전 요구사항

-   Python 3.8 이상
-   MySQL 8.0 이상
-   Git

### 2. 설치 및 실행

1.  **저장소 복제**
    ```bash
    git clone https://github.com/your-username/HotSpot-Project.git
    cd HotSpot-Project
    ```

2.  **가상 환경 생성 및 활성화**
    ```bash
    python -m venv venv
    # Windows
    venv\Scripts\activate
    # macOS/Linux
    source venv/bin/activate
    ```

3.  **의존성 패키지 설치**
    ```bash
    pip install -r requirements.txt
    ```

4.  **환경 변수 설정**
    - `category/` 폴더 내에 `keys.json` 파일을 생성하고 아래와 같이 Kakao API 키를 입력합니다.
      ```json
      {
        "KAKAO_API": "YOUR_KAKAO_API_KEY_HERE"
      }
      ```

5.  **데이터베이스 설정**
    - 로컬 MySQL 서버에 `testdatabase` 스키마를 생성합니다.
    - `category/category/settings.py` 파일의 `DATABASES` 부분을 실제 DB 환경에 맞게 수정합니다.

6.  **데이터베이스 마이그레이션**
    ```bash
    python category/manage.py migrate
    ```

7.  **개발 서버 실행**
    ```bash
    python category/manage.py runserver
    ```

8.  웹 브라우저에서 `http://127.0.0.1:8000/` 주소로 접속합니다.

## 📂 프로젝트 구조

```
HotSpot-Project/
├── category/              # Django 프로젝트 루트
│   ├── manage.py          # Django 관리 스크립트
│   ├── myApp/             # Django 앱
│   │   ├── views.py       # 뷰 로직
│   │   ├── models.py      # 데이터베이스 모델
│   │   ├── templates/     # HTML 템플릿
│   │   └── static/        # CSS, JS, 이미지 등 정적 파일
│   ├── crawling/          # 데이터 크롤링 스크립트
│   └── util/              # 데이터 처리 유틸리티 (e.g., DB 삽입)
├── *.ipynb                # 데이터 분석 및 모델링 노트북
├── requirements.txt       # Python 의존성 목록
└── README.md              # 프로젝트 설명서
```
