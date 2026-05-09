<script setup>
import { computed } from 'vue';
import Error from '@/components/Error.vue';
import DayCard from '@/components/DayCard.vue';
import CitySelect from '@/components/CitySelect.vue';
import Stat from '@/components/Stat.vue';
import { errorMap } from '@/constans';

const { error, data, activeIndex } = defineProps({
  error: Object,
  data: Object,
  activeIndex: Number,
});

const emit = defineEmits(['select-index', 'select-city']);

const statData = computed(() => {
  if (!data) {
    return [];
  }
  return [
    {
      label: 'Влажность',
      stat: data.forecast.forecastday[activeIndex].day.avghumidity + ' %',
    },
    {
      label: 'Вероятность дождя',
      stat:
        data.forecast.forecastday[activeIndex].day.daily_chance_of_rain + ' %',
    },
    {
      label: 'Ветер',
      stat:
        Math.round(
          data.forecast.forecastday[activeIndex].day.maxwind_kph / 3.6,
        ) + ' м/с',
    },
  ];
});

const errorDisplay = computed(() => {
  return errorMap.get(error?.error?.code);
});
</script>

<template>
  <Error v-if="error" :error="errorDisplay" />
  <div v-if="data?.current" class="stat-data">
    <div class="stat-list">
      <Stat v-for="item in statData" v-bind="item" :key="item.label" />
    </div>
    <div class="day-card-list">
      <DayCard
        v-for="(item, i) in data.forecast.forecastday"
        :key="item.date"
        :weather-code="item.day.condition.code"
        :temp="item.day.avgtemp_c"
        :data="new Date(item.date)"
        :is-active="activeIndex === i"
        @click="() => emit('select-index', i)"
      />
    </div>
  </div>
  <CitySelect />
</template>
<style scoped>
.day-card-list {
  display: flex;
  justify-content: space-around;
  gap: 5px;
  background-color: var(--color-bg-card);
  border-radius: 10px;
}

.stat-data {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 80px;
  margin-bottom: 70px;
}

.stat-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}
</style>
