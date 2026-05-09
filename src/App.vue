<script setup>
import { onMounted, provide, ref, watch } from 'vue';
import PaneRight from '@/components/PaneRight.vue';
import { cityProvide, API_ENDPOINT } from './constans';
import PaneLeft from '@/components/PaneLeft.vue';

let data = ref();
let error = ref();
let activeIndex = ref(0);
let city = ref('Москва');
provide(cityProvide, city);

watch(city, () => {
  getCity(city.value);
});

onMounted(() => {
  getCity(city.value);
});

async function getCity(city) {
  const params = new URLSearchParams({
    q: city,
    lang: 'ru',
    key: '679a9edc72784ce1ae6145806262802',
    days: 3,
  });
  const res = await fetch(`${API_ENDPOINT}/forecast.json?${params.toString()}`);
  if (res.status != 200) {
    error.value = await res.json();
    data.value = null;
    return;
  }
  error.value = null;
  data.value = await res.json();
}
</script>

<template>
  <main class="main">
    <div class="left">
      <PaneLeft
        v-if="data"
        :day-data="data.forecast.forecastday[activeIndex]"
      />
    </div>
    <div class="right">
      <PaneRight
        :data
        :error
        :active-index="activeIndex"
        @select-index="(i) => (activeIndex = i)"
        @select-city="getCity"
      />
    </div>
  </main>
</template>

<style scoped>
.main {
  display: flex;
  align-items: center;
  justify-content: center;
}
.left {
  width: 500px;
  height: 680px;
  border-radius: 30px;
  background-image: url('../public/bg.png');
  background-repeat: no-repeat;
  background-size: cover;
}
.right {
  position: relative;
  min-height: 623px;
  background: var(--color-bg-main);
  padding: 60px 50px;
  border-radius: 0 25px 25px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: end;
}
</style>
