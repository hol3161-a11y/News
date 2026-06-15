<template>
  <div class="news">
    <header>
      <div class="news-title">
        <p>Now News</p>
        <h1>오늘의 실시간 뉴스</h1>
        <small>최신 뉴스를 빠르고 정확하게 전해드립니다.</small>
      </div>
      <div class="search">
        <form @submit.prevent="newsList">
          <input type="text" v-model="keyword" placeholder="뉴스 검색" />
          <button type="submit"><img src="../image/ic_search.svg" /></button>
        </form>
      </div>
    </header>
    <div class="category">
      <button
        :class="{ active: keyword === '전체' }"
        @click="keywordChange('전체')"
      >
        전체
      </button>
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
        IT
      </button>
    </div>
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

    <ul v-else>
      <li v-for="(item, idx) in data" :key="idx">
        <p><img :src="item.image" /></p>
        <div>
          <h2 v-html="item.title"></h2>
          <p v-html="item.description"></p>
        </div>
        <div class="footer">
          <a :href="item.link" target="_blank" rel="noopener noreferrer">
            자세히 보기 ->
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
      loading: false,
    };
  },

  methods: {
    keywordChange(value) {
      this.keyword = value;
      this.newsList();
    },

    newsList() {
      this.loading = true;

      const searchKeyword = this.keyword === "전체" ? "뉴스" : this.keyword;

      fetch(`https://react-todo-u1jz.vercel.app/news?keyword=${searchKeyword}`)
        .then((res) => res.json())
        .then((res) => {
          this.data = res;
        })
        .finally(() => {
          this.loading = false;
        });
    },

    formatDate(dateString) {
      const date = new Date(dateString);
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, "0");
      const day = String(date.getDate()).padStart(2, "0");
      const week = ["일", "월", "화", "수", "목", "금", "토"];
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
    margin-top: 32px;
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
  margin-top: 36px;

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
  }
}

.category {
  margin: 13px 0 20px;
  display: flex;
  justify-content: flex-end;
  gap: 14px;

  button {
    width: 5%;
    background-color: #fff;
    padding: 3px;
    color: #111827;
    border: 1px solid #e4e7ec;
    border-radius: 999px;
    font-size: 16px;
    font-weight: 500;
    cursor: pointer;
    transition: 0.2s ease;

    &:hover {
      background-color: #00d1c1;
      border: 1px solid #00d1c1;
      color: #fff;
      transform: translateY(-2px);
    }

    &.active {
      background-color: #7a5cfa;
      border: 1px solid #7a5cfa;
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

  cursor: pointer;

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
    h1 {
      font-size: 42px;
    }
  }

  .search {
    margin-top: 0;

    button[type="submit"] {
      width: 5%;
    }
  }

  .category {
    justify-content: flex-start;
    flex-wrap: wrap;

    button {
      width: auto;
      padding: 8px 18px;
    }
  }

  ul {
    grid-template-columns: repeat(2, 1fr);
  }

  li {
    height: 650px;
  }
}

/* 모바일 */
@media (max-width: 768px) {
  body {
    margin: 0 20px 60px;
  }

  .news-title {
    p {
      font-size: 24px;
    }

    h1 {
      font-size: 34px;
    }

    small {
      font-size: 14px;
    }
  }

  .search {
    input[type="text"] {
      padding: 14px;
    }

    button[type="submit"] {
      width: 50px;
    }
  }

  .category {
    gap: 8px;

    button {
      font-size: 14px;
      padding: 7px 14px;
    }
  }

  ul {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  li {
    height: auto;
    min-height: 560px;

    img {
      height: 240px;
    }

    h2 {
      font-size: 21px;
    }

    p {
      font-size: 15px;
      -webkit-line-clamp: 4;
    }
  }

  .footer {
    a {
      position: static;
    }
  }
}

/* 작은 모바일 */
@media (max-width: 480px) {
  body {
    margin: 0 14px 50px;
  }

  .news-title {
    h1 {
      font-size: 28px;
    }
  }

  .search {
    form {
      gap: 6px;
    }

    button[type="submit"] {
      width: 46px;
    }
  }

  .category {
    button {
      font-size: 13px;
      padding: 6px 12px;
    }
  }

  li {
    border-radius: 18px;

    img {
      height: 210px;
    }

    div {
      padding: 18px;
    }

    h2 {
      font-size: 19px;
    }
  }
}
</style>
