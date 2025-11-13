<template>
  <div>
    <h1>GitHub 조직 커밋 잔디</h1>

    <div v-if="isLoading" class="loading">
      데이터 로딩 중...
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="!isLoading && !error" class="container">
      <div v-for="(repo, repoName) in commitData" :key="repoName" class="repo-section">
        <h3>{{ repoName }}</h3>
        <calendar-heatmap
          :values="repo.values"
          :end-date="endDate"
          tooltip-unit="commits"
          :round="2"
          :range-color="['#ebedf0', '#9be9a8', '#40c463', '#30a14e', '#216e39']"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { CalendarHeatmap } from 'vue-calendar-heatmap';

// 1. 상태 변수
const commitData = ref({});
const isLoading = ref(true);
const error = ref(null);
const endDate = ref(new Date().toISOString().split('T')[0]); // 오늘 날짜

// 2. 마운트 시 데이터 로드
onMounted(async () => {
  try {
    // GitHub API가 아닌, 우리 앱과 함께 배포된 public/commits.json을 로드
    // './'는 배포된 사이트의 루트를 의미
    const response = await axios.get('./commits.json'); 

    commitData.value = response.data;

    if (Object.keys(commitData.value).length === 0) {
      error.value = '표시할 커밋 데이터가 없습니다.';
    }

  } catch (err) {
    console.error('데이터 로드 실패:', err);
    error.value = '커밋 데이터를 불러오는 데 실패했습니다.';
  } finally {
    isLoading.value = false;
  }
});
</script>

<style>
/* (간단한 스타일) */
.container { padding: 20px; }
.repo-section { margin-bottom: 40px; }
.loading, .error { text-align: center; padding: 50px; font-size: 1.2em; }
.error { color: red; }
</style>