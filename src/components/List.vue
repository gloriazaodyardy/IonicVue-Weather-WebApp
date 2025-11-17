<template>
  <ion-list>
    <ion-item v-for="(item, i) in displayedWeather" :key="i">
      <ion-label class="weather-item-label">
        <b>{{ item.time }}</b>
        <p>{{ item.temperature }} °C</p>
      </ion-label>
    </ion-item>
  </ion-list>
  <ion-infinite-scroll @ionInfinite="loadMore">
    <ion-infinite-scroll-content></ion-infinite-scroll-content>
  </ion-infinite-scroll>
</template>

<script setup lang="ts">
import {
  IonList,
  IonItem,
  IonLabel,
  IonInfiniteScroll,
  IonInfiniteScrollContent,
  InfiniteScrollCustomEvent,
} from "@ionic/vue";
import { ref, onMounted } from "vue";

//   Fetch API data
interface weatherItem {
  time: string;
  temperature: number;
}

const weatherData = ref<weatherItem[]>([]);
// Apply infinite scroll
const displayedWeather = ref<weatherItem[]>([]);
const pageSize = 10;
const APIURL ="https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m";


onMounted(async () => {
  try {
    const response = await fetch(APIURL);
    const data = await response.json();

    // pisah kedua data dalam 2 data array di variabel berbeda
    const times: string[] = data.hourly.time;
    const temperatures: number[] = data.hourly.temperature_2m;

    // gabung kedua data ke dalam array of object
    const mapData = times.map((time, i) => ({
      time: new Date(time).toLocaleString("id", {
        hour: "2-digit",
        minute: "2-digit",
        day: "2-digit",
        month: "short",
        year: "numeric",
      }),
      temperature: temperatures[i],
    }));

    weatherData.value = mapData;
    displayedWeather.value = weatherData.value.slice(0, pageSize);
  } catch (error) {
    console.error("Failed to fetch weather data:", error);
  }
});

// function to load more data on scroll
const loadMore = (event: InfiniteScrollCustomEvent) => {
  const currentLength = displayedWeather.value.length;
  const nextEndIndex = currentLength + pageSize;
  const nextChunk = weatherData.value.slice(currentLength, nextEndIndex);

  displayedWeather.value = [...displayedWeather.value, ...nextChunk];

  event.target.complete();

  // Cek apakah semua data sudah dimuat
  if (nextEndIndex >= weatherData.value.length) {
    event.target.disabled = true;
  }
};
</script>

<style scoped>
ion-list {
border-radius: 20px;
}

.weather-item-label {
  gap: 1rem;
  display: flex;
  flex-direction: column;
  align-items: flex-start;

  p {
    color: #8c8c8c;
  }
}

</style>