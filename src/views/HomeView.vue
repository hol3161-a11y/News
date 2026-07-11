<template>
  <div class="news">
    <header>
      <div class="news-title">
        <p>Now News</p>
        <h1>오늘의 실시간 뉴스</h1>
        <small>최신 뉴스를 빠르고 정확하게 전해드립니다.</small>
      </div>

      <div class="search">
        <form @submit.prevent="searchNews">
          <input
            type="text"
            v-model="searchText"
            placeholder="뉴스 검색"
          />

          <button type="submit">
            <img src="../image/ic_search.svg" alt="검색" />
          </button>
        </form>
      </div>
    </header>

    <div class="category">
      <button
        class="all"
        :class="{ active: keyword === '전체' }"
        @click="keywordChange('전체')"
      >
        전체
      </button>

      <div class="divider"></div>

      <div class="category-list">
        <button
          :class="{ active: keyword === '정치' }"
          @click="keywordChange('정치')"
        >
          정치
        </button>

        <button
          :class="{ active: keyword === '경제' }"
          @click="keywordChange('경제')"
        >
          경제
        </button>

        <button
          :class="{ active: keyword === '사회' }"
          @click="keywordChange('사회')"
        >
          사회
        </button>

        <button
          :class="{ active: keyword === '세계' }"
          @click="keywordChange('세계')"
        >
          세계
        </button>

        <button
          :class="{ active: keyword === 'IT 개발' }"
          @click="keywordChange('IT 개발')"
        >
          IT 개발
        </button>
      </div>
    </div>

    <!-- 로딩 중 -->
    <ul v-if="loading" class="skeleton-list">
      <li v-for="n in 4" :key="n" class="skeleton-card">
        <div class="skeleton-img"></div>

        <div class="skeleton-content">
          <div class="skeleton-title"></div>
          <div class="skeleton-title short"></div>
          <div class="skeleton-text"></div>
          <div class="skeleton-text"></div>
          <div class="skeleton-text short"></div>
        </div>

        <div class="skeleton-footer">
          <div></div>
          <div></div>
        </div>
      </li>
    </ul>

    <!-- 뉴스 목록 -->
    <ul v-else>
      <li v-for="(item, idx) in data" :key="idx">
        <p>
          <img :src="item.image" :alt="item.title" />
        </p>

        <div>
          <h2 v-html="item.title"></h2>
          <p v-html="item.description"></p>
        </div>

        <div class="footer">
          <a
            :href="item.link"
            target="_blank"
            rel="noopener noreferrer"
          >
            자세히 보기 →
          </a>

          <small class="date">
            {{ formatDate(item.pubDate) }}
          </small>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "HomeView",

  data() {
    return {
      data: [],
      keyword: "전체",
      searchText: "",
      loading: false,
    };
  },

  methods: {
    keywordChange(value) {
      this.keyword = value;

      if (value === "전체") {
        this.searchText = "";
      } else {
        this.searchText = value;
      }

      this.newsList();
    },

    searchNews() {
      const searchKeyword = this.searchText.trim();

      if (!searchKeyword) {
        return;
      }

      this.keyword = "";

      this.newsList();
    },

    async newsList() {
      this.loading = true;
      this.data = [];

      try {
        if (this.searchText.trim()) {
          const response = await fetch(
            `https://react-todo-u1jz.vercel.app/news?keyword=${encodeURIComponent(
              this.searchText.trim()
            )}`
          );

          if (!response.ok) {
            throw new Error(`검색 요청 실패: ${response.status}`);
          }

          const result = await response.json();

          this.data = Array.isArray(result)
            ? result
            : result.items || [];

          return;
        }

        if (this.keyword === "전체") {
          const keywords = [
            "정치",
            "경제",
            "사회",
            "세계",
            "IT 개발",
            "스포츠",
            "문화",
            "연예",
          ];

          const mixedNews = [];

          for (const keyword of keywords) {
            try {
              const response = await fetch(
                `https://react-todo-u1jz.vercel.app/news?keyword=${encodeURIComponent(
                  keyword
                )}`
              );

              if (!response.ok) {
                console.warn(
                  `${keyword} 요청 실패: ${response.status}`
                );
                continue;
              }

              const result = await response.json();

              const newsList = Array.isArray(result)
                ? result
                : result.items || [];

              mixedNews.push(...newsList.slice(0, 2));

              await new Promise((resolve) =>
                setTimeout(resolve, 300)
              );
            } catch (error) {
              console.error(`${keyword} 뉴스 오류:`, error);
            }
          }

          // 뉴스 순서 섞기
          this.data = mixedNews.sort(
            () => Math.random() - 0.5
          );

          return;
        }
      } catch (error) {
        console.error("뉴스 불러오기 오류:", error);
        this.data = [];
      } finally {
        this.loading = false;
      }
    },

    formatDate(dateString) {
      const date = new Date(dateString);

      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(
        2,
        "0"
      );
      const day = String(date.getDate()).padStart(2, "0");

      const week = [
        "일",
        "월",
        "화",
        "수",
        "목",
        "금",
        "토",
      ];

      const dayName = week[date.getDay()];

      return `${year}.${month}.${day} (${dayName})`;
    },
  },

  mounted() {
    this.newsList();
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #f6f7fa;
  margin: 0 120px 120px;
}

header {
  display: flex;
  justify-content: space-between;
}

.news-title {
  text-align: left;

  p {
    margin-top: 45px;
    font-size: 32px;
    font-weight: 800;
    color: #7a5cfa;
    padding-bottom: 7px;
  }

  h1 {
    font-size: 52px;
    font-weight: bold;
    padding-bottom: 5px;
  }

  small {
    font-size: 16px;
    font-weight: 600;
    color: #667085;
  }
}

.search {
  margin-top: 48px;

  form {
    display: flex;
    align-items: center;
    gap: 3px;
  }

  input {
    background-color: #fff;
    border: 1px solid #e4e7ec;
    border-radius: 14px;
  }

  input[type="text"] {
    width: 100%;
    padding: 15px 40px 15px 13px;
    outline: none;

    &::placeholder {
      color: #98a2b3;
      font-size: 16px;
    }
  }

button[type="submit"] {
  width: 22%;
  background-color: #fff;
  border: 1px solid #e4e7ec;
  padding: 6px 0;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.15s ease;

  &:hover {
    background-color: #f5f5f5; 
    transform: translateY(-1px);
  }

  &:active {
    background-color: #eceff3; 
    transform: translateY(1px) scale(0.98); 
    box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.08);
  }
}
}

.category {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 12px;
  margin: 20px 0 30px;
}

.all {
  background: #7a5cfa;
  color: #fff;
  border: none;
  border-radius: 999px;
  padding: 10px 24px;
  font-size: 15px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.2s ease;

  &:hover {
    background: #5631eb; 
    transform: translateY(-2px);
  }

  &:active {
    background: #5631eb; 
    transform: translateY(1px) scale(0.98); 
  }

  &.active {
    background: #7a5cfa;
  }
}

.divider {
  width: 1px;
  height: 28px;
  background: #e4e7ec;
}

.category-list {
  display: flex;
  gap: 10px;

  button {
    background: #fff;
    border: 1px solid #e4e7ec;
    border-radius: 999px;
    padding: 10px 18px;

    color: #667085;
    font-size: 15px;
    font-weight: 600;

    cursor: pointer;
    transition: all 0.25s ease;

    &:hover {
      border-color: #00d1c1;
      color: #00d1c1;
      transform: translateY(-2px);
    }

    &.active {
      background: #00d1c1;
      border-color: #00d1c1;
      color: #fff;
    }
  }
}

ul {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 28px;
}

li {
  position: relative;
  overflow: hidden;
  background: #fff;

  border-radius: 24px;

  transition: 0.3s;

  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.04);

  &:hover {
    transform: translateY(-8px);
  }

  img {
    width: 100%;
    height: 300px;

    object-fit: cover;
  }

  div {
    padding: 24px 24px 150px;
  }

  h2 {
    display: block;

    font-size: 24px;
    text-align: center;
    line-height: 1.4;

    margin-bottom: 20px;

    color: #111827;
  }

  p {
    display: -webkit-box;
    -webkit-line-clamp: 5;
    -webkit-box-orient: vertical;
    overflow: hidden;

    font-size: 16px;
    font-weight: 300;
    text-align: left;
    line-height: 1.7;
    color: #667085;
  }
}

.footer {
  position: absolute;
  left: 24px;
  right: 24px;
  bottom: 24px;

  display: flex;
  justify-content: space-between;
  align-items: center;

  margin-top: 0;
  padding: 0;

  a {
    color: #7a5cfa;
    font-size: 16px;
    font-weight: 600;

    &:hover {
      color: #00d1c1;
    }
  }
}

.date {
  font-weight: 700;
  color: #98a2b3;
}

.skeleton-card {
  height: 650px;
  cursor: default;

  &:hover {
    transform: none;
  }
}

.skeleton-img,
.skeleton-title,
.skeleton-text,
.skeleton-footer div {
  background: linear-gradient(90deg, #eef0f5 25%, #f8f9fc 50%, #eef0f5 75%);
  background-size: 200% 100%;
  animation: skeletonLoading 1.4s infinite;
}

.skeleton-img {
  width: 100%;
  height: 300px;
}

.skeleton-content {
  padding: 24px;
}

.skeleton-title {
  width: 90%;
  height: 24px;
  margin: 0 auto 14px;
  border-radius: 8px;

  &.short {
    width: 65%;
    margin-bottom: 28px;
  }
}

.skeleton-text {
  width: 100%;
  height: 14px;
  margin-bottom: 12px;
  border-radius: 8px;

  &.short {
    width: 70%;
  }
}

.skeleton-footer {
  position: absolute;
  left: 24px;
  right: 24px;
  bottom: 24px;

  display: flex;
  justify-content: space-between;

  div {
    width: 90px;
    height: 16px;
    border-radius: 8px;
  }
}

@keyframes skeletonLoading {
  0% {
    background-position: 200% 0;
  }

  100% {
    background-position: -200% 0;
  }
}

/* =========================
   RESPONSIVE
========================= */

/* 작은 노트북 */
@media (max-width: 1200px) {
  body {
    margin: 0 60px 100px;
  }

  ul {
    grid-template-columns: repeat(3, 1fr);
    gap: 24px;
  }

  li {
    img {
      height: 260px;
    }

    h2 {
      font-size: 22px;
    }
  }
}

/* 태블릿 */
@media (max-width: 1024px) {
  body {
    margin: 0 40px 80px;
  }

  header {
    flex-direction: column;
    gap: 24px;
  }

  .news-title {
    p {
      margin-top: 28px;
      font-size: 28px;
    }

    h1 {
      font-size: 42px;
    }
  }

  .search {
    width: 100%;
    margin-top: 0;

    form {
      width: 100%;
      gap: 8px;
    }

    input[type="text"] {
      flex: 1;
      width: auto;
    }

    button[type="submit"] {
      flex-shrink: 0;
      width: 56px;
    }
  }

  .category {
    justify-content: flex-start;
    flex-wrap: wrap;
    margin: 22px 0 28px;
  }

  .all {
    padding: 9px 22px;
  }

  .category-list {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;

    button {
      padding: 9px 17px;
    }
  }

  ul {
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }

  li {
    img {
      height: 270px;
    }

    div {
      padding: 22px 22px 120px;
    }
  }
}

/* 모바일 */
@media (max-width: 768px) {
  body {
    margin: 0 20px 60px;
  }

  header {
    gap: 20px;
  }

  .news-title {
    p {
      margin-top: 24px;
      padding-bottom: 5px;
      font-size: 24px;
    }

    h1 {
      font-size: 34px;
      line-height: 1.3;
    }

    small {
      font-size: 14px;
      line-height: 1.5;
    }
  }

  .search {
    form {
      gap: 7px;
    }

    input[type="text"] {
      min-width: 0;
      padding: 14px 16px;
      font-size: 15px;
    }

    button[type="submit"] {
      width: 50px;
      padding: 7px 0;

      img {
        width: 24px;
        height: 24px;
      }
    }
  }

  .category {
    align-items: flex-start;
    gap: 10px;
    margin: 18px 0 24px;
  }

  .all {
    flex-shrink: 0;
    padding: 8px 18px;
    font-size: 14px;
  }

  .divider {
    height: 34px;
  }

  .category-list {
    gap: 8px;

    button {
      padding: 8px 14px;
      font-size: 14px;
    }
  }

  ul {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  li {
    min-height: 560px;
    border-radius: 20px;

    img {
      height: 240px;
    }

    div {
      padding: 20px 20px 110px;
    }

    h2 {
      margin-bottom: 16px;
      font-size: 21px;
    }

    p {
      font-size: 15px;
      line-height: 1.65;
      -webkit-line-clamp: 4;
    }
  }

  .footer {
    left: 20px;
    right: 20px;
    bottom: 20px;

    a {
      font-size: 15px;
    }
  }

  .date {
    font-size: 13px;
  }

  .skeleton-card {
    height: 560px;
  }

  .skeleton-img {
    height: 240px;
  }
}

/* 작은 모바일 */
@media (max-width: 480px) {
  body {
    margin: 0 14px 50px;
  }

  .news-title {
    p {
      font-size: 21px;
    }

    h1 {
      font-size: 28px;
    }

    small {
      font-size: 13px;
    }
  }

  .search {
    form {
      gap: 6px;
    }

    input[type="text"] {
      padding: 13px 14px;
      border-radius: 12px;
    }

    button[type="submit"] {
      width: 46px;
      border-radius: 12px;
    }
  }

  .category {
    display: grid;
    grid-template-columns: auto 1px 1fr;
    align-items: start;
    gap: 10px;
  }

  .all {
    padding: 7px 15px;
    font-size: 13px;
  }

  .divider {
    width: 1px;
    height: 32px;
  }

  .category-list {
    display: flex;
    flex-wrap: wrap;
    gap: 7px;

    button {
      padding: 7px 12px;
      font-size: 13px;
    }
  }

  li {
    min-height: 520px;
    border-radius: 18px;

    img {
      height: 210px;
    }

    div {
      padding: 18px 18px 105px;
    }

    h2 {
      font-size: 19px;
      line-height: 1.45;
    }

    p {
      font-size: 14px;
      -webkit-line-clamp: 4;
    }
  }

  .footer {
    left: 18px;
    right: 18px;
    bottom: 18px;
    gap: 10px;

    a {
      font-size: 14px;
    }
  }

  .date {
    font-size: 12px;
    white-space: nowrap;
  }

  .skeleton-card {
    height: 520px;
  }

  .skeleton-img {
    height: 210px;
  }
}
</style>
