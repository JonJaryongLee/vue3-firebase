<script setup>
/**
 * @typedef Article
 * @type {object}
 * @property {string} articleId - 게시글의 고유 식별자.
 * @property {string} title - 게시글의 제목.
 * @property {string} content - 게시글의 내용.
 * @property {string} date - 게시글의 날짜 (dayjs로 변환된 문자열).
 */

import { ref } from "vue";
// npm i firebase
import { initializeApp } from "firebase/app";
import { getFirestore, getDocs, collection } from "firebase/firestore";
// npm i dayjs
import dayjs from "dayjs";

/**
 * @type {ref<Article[]>}
 * @description Article 객체의 배열을 저장하는 Vue Ref 인스턴스.
 */
const articles = ref([]);

// 이 부분을 교체하세요
const firebaseConfig = {};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// 전체 가져오기
async function fetchArticles() {
  try {
    const querySnapshot = await getDocs(collection(db, "articles"));
    articles.value = querySnapshot.docs.map((doc) => ({
      articleId: doc.id,
      ...doc.data(),
      date: dayjs(doc.data().date.toDate()).format(
        "YYYY년 MM월 DD일 HH시 mm분 ss초"
      ),
    }));
  } catch (error) {
    console.error(error);
  }
}

fetchArticles();
</script>

<template>
  <h1>게시판</h1>
  <ul v-if="articles.length">
    <li v-for="article in articles" :key="article.articleId">
      <h2>{{ article.title }}</h2>
      <p>{{ article.content }}</p>
      <p>{{ article.date }}</p>
    </li>
  </ul>
  <div v-else>loading...</div>
</template>
