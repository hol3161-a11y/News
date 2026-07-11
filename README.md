# 📰 Now News

> 네이버 뉴스 API를 활용해 최신 뉴스를 검색하고 카테고리별로 확인할 수 있는 뉴스 검색 서비스입니다.

<br />

## 🔗 프로젝트 링크

- **Demo** : [배포 링크](https://news-beryl.vercel.app/)
- **GitHub** : [GitHub 저장소 링크](https://github.com/hol3161-a11y/News)

<br />

## 📌 프로젝트 소개

Now News는 사용자가 원하는 키워드의 최신 뉴스를 검색하고, 정치·경제·사회·생활·IT·세계 등 다양한 카테고리의 뉴스를 확인할 수 있는 뉴스 검색 서비스입니다.

네이버 뉴스 API를 활용하여 최신 뉴스 데이터를 불러오며, 뉴스 카드를 클릭하면 원문 기사 페이지로 이동할 수 있도록 구현했습니다.

<br />

## 📅 개발 기간

- **개발 기간** : 2026.04.18 (1일)
- **개발 인원** : 1명 (UI 디자인 · 프론트엔드 개발 · API 연동)
- **프로젝트 유형** : 개인 프로젝트

<br />

## 🛠 기술 스택

### Frontend

- Vue
- JavaScript
- HTML5
- CSS3

### API

- Naver News API

### Tools

- Git
- GitHub
- Visual Studio Code

<br />

## ✨ 주요 기능

### 🔍 뉴스 검색

- 키워드를 입력하여 원하는 뉴스 검색
- 네이버 뉴스 API를 활용한 실시간 뉴스 조회
- 검색 결과를 카드 형태로 출력

### 🗂 카테고리별 뉴스 조회

- 전체
- 정치
- 경제
- 사회
- 생활
- 세계
- IT

카테고리를 선택하여 원하는 분야의 뉴스를 확인할 수 있습니다.

### 📰 뉴스 정보 제공

- 뉴스 제목
- 뉴스 이미지
- 뉴스 요약
- 기사 작성일

### 🔗 원문 기사 이동

- 뉴스 카드를 클릭하면 원문 기사 페이지로 이동

### 📱 반응형 UI

- PC / Tablet / Mobile 환경 지원
- 화면 크기에 따라 뉴스 카드 레이아웃 변경

<br />

## 💻 구현 내용

- Vue 컴포넌트 기반 화면 구성
- 네이버 뉴스 API 연동
- 키워드 검색 기능 구현
- 카테고리별 뉴스 조회 기능
- 전체 카테고리 랜덤 뉴스 구성
- Skeleton UI를 활용한 로딩 화면 구현
- 기사 작성일 포맷 변환
- 뉴스 원문 페이지 이동
- 반응형 레이아웃 구현

<br />


## 📂 프로젝트 구조

```bash
NEWS_1
├── public
│   ├── faviconico.png
│   └── index.html
├── src
│   ├── assets
│   │   └── logo.png
│   ├── components
│   │   └── HelloWorld.vue
│   ├── image
│   │   └── ic_search.svg
│   ├── router
│   │   └── index.js
│   ├── store
│   │   └── index.js
│   ├── views
│   │   ├── HomeView.vue
│   │   └── AboutView.vue
│   ├── App.vue
│   └── main.js
├── .gitignore
├── babel.config.js
├── jsconfig.json
├── package.json
├── package-lock.json
├── README.md
└── vue.config.js
```

<br />

## 👨‍💻 담당 개발

**1인 프로젝트**

- **UI 디자인**
  - 메인 화면 레이아웃 및 반응형 UI 설계
  - 뉴스 카드 및 카테고리 UI 디자인

- **프론트엔드 개발**
  - Vue 기반 컴포넌트 설계 및 개발
  - Vue Router를 활용한 페이지 구성
  - 뉴스 검색 및 카테고리 필터 기능 구현
  - 뉴스 카드 UI 및 원문 기사 이동 기능 구현
  - 반응형 레이아웃 구현

- **API 연동**
  - 네이버 뉴스 API 연동
  - 키워드 검색 기능 구현
  - 카테고리별 뉴스 데이터 조회
  - API 응답 데이터 가공 및 화면 렌더링

<br />

# 🛠 트러블 슈팅

## 📰 전체 카테고리에서 다양한 뉴스 제공

### 문제 상황

처음에는 `전체` 버튼을 클릭하면 `"뉴스"` 키워드 하나로만 검색하여 특정 분야의 뉴스가 반복적으로 노출되었습니다.

### 해결 방법

정치, 경제, 사회, 세계, IT, 스포츠, 문화, 연예 등 여러 카테고리의 뉴스를 순차적으로 조회한 뒤 하나의 배열로 합쳐 랜덤하게 출력하도록 개선했습니다.

```javascript
if (this.keyword === "전체") {
  const keywords = [
    "정치",
    "경제",
    "사회",
    "세계",
    "IT",
    "스포츠",
    "문화",
    "연예",
  ];

  const mixedNews = [];

  for (const keyword of keywords) {
    const response = await fetch(
      `https://react-todo-u1jz.vercel.app/news?keyword=${encodeURIComponent(
        keyword
      )}`
    );

    const result = await response.json();

    const newsList = Array.isArray(result)
      ? result
      : result.items || [];

    mixedNews.push(...newsList.slice(0, 2));
  }

  this.data = mixedNews.sort(() => Math.random() - 0.5);
}
```

### 결과

- 다양한 분야의 뉴스를 한 화면에서 제공
- 특정 분야에 치우치지 않는 뉴스 구성
- 전체 카테고리의 활용성 향상

---

## 🔍 검색어와 카테고리 상태 관리

### 문제 상황

카테고리 버튼을 선택한 뒤 검색을 실행하면 기존 카테고리 버튼이 계속 활성화되어 현재 검색 기준을 직관적으로 알기 어려웠습니다.

### 해결 방법

검색 입력값과 카테고리 상태를 분리하여 관리하고, 검색 실행 시 카테고리 활성화를 해제하도록 수정했습니다.

```javascript
data() {
  return {
    keyword: "전체",
    searchText: "",
  };
}
```

```javascript
searchNews() {
  if (!this.searchText.trim()) return;

  this.keyword = "";
  this.newsList();
}
```

---

## ⏳ 뉴스 로딩 화면 개선

### 문제 상황

API 응답을 기다리는 동안 화면이 비어 있어 사용자가 로딩 상태를 확인하기 어려웠습니다.

### 해결 방법

`loading` 상태를 관리하고, API 요청 중에는 Skeleton UI를 출력한 뒤 응답 완료 후 실제 뉴스 목록을 렌더링하도록 구현했습니다.

```javascript
async newsList() {
  this.loading = true;

  try {
    // API 요청
  } finally {
    this.loading = false;
  }
}
```

```vue
<ul v-if="loading" class="skeleton-list">
  ...
</ul>

<ul v-else>
  ...
</ul>
```

### 결과

- 검색 결과가 즉시 갱신됨
- 이전 검색 데이터가 남지 않음
- 검색 기능의 사용성이 향상됨

<br />

## 🎯 프로젝트를 통해 배운 점

이번 프로젝트를 통해 Vue 컴포넌트 기반 개발과 외부 API를 연동하는 방법을 익힐 수 있었습니다.

또한 비동기 데이터 처리와 상태 관리를 경험하며 검색 기능과 카테고리 기능을 구현하는 과정에서 사용자 경험(UX)의 중요성을 배울 수 있었습니다.

특히 전체 카테고리의 뉴스 구성 방식, 검색과 카테고리 상태 관리, Skeleton UI를 활용한 로딩 처리 등을 구현하며 기능 구현뿐만 아니라 사용자가 자연스럽게 서비스를 이용할 수 있는 흐름을 고민하는 경험을 할 수 있었습니다.
