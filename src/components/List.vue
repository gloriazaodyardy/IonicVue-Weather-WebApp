<template>
  <ion-list>
    <ion-item v-for="(item, i) in displayedWeather" :key="i">
      <ion-label class="weather-item-label">
        <strong>{{ item.time }} GMT</strong>
        <strong>{{ item.temperature }}°C</strong>
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
import { ref, onMounted, watch, computed } from "vue";

// Define weather data structure
interface weatherItem {
  time: string;
  temperature: number;
}

const weatherData = ref<weatherItem[]>([]); //main storage for weather data
const displayedWeather = ref<weatherItem[]>([]); //data displayed on the screen
const APIURL ="https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m";
const pageSize = 10; //number of data to load per scroll

///// FILTERING FUNCTION /////

// Apply filter from parent component
const props = defineProps<{ filterQuery: string }>();

// Filtered weather data based on search query
const filteredWeatherData = computed(() => {
  const query = props.filterQuery;
  const sourceData = weatherData.value;

  if (query) {
    // Filter data based on query
    return sourceData.filter((item) => item.time.toLowerCase().includes(query));
  }
  // If no query, return all data
  return sourceData;
});
// Watch for changes in filtered data to reset displayed data
watch(filteredWeatherData, () => {
  displayedWeather.value = filteredWeatherData.value.slice(0, pageSize);
});

///// DATA FETCHING AND INFINITE SCROLL /////

// Fetch data on component mount
onMounted(async () => {
  try {
    const response = await fetch(APIURL);
    const data = await response.json();

    // extract relevant data
    const times: string[] = data.hourly.time;
    const temperatures: number[] = data.hourly.temperature_2m;

    // map data to weatherItem format
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

    // save to weatherData
    weatherData.value = mapData;
    displayedWeather.value = weatherData.value.slice(0, pageSize);
  } catch (error) {
    console.error("Failed to fetch weather data:", error);
  }
});

///// Infinite Scroll Handler /////

// function to load more data on scroll
const loadMore = (event: InfiniteScrollCustomEvent) => {
  const currentLength = displayedWeather.value.length;
  const nextEndIndex = currentLength + pageSize;
  const nextChunk = weatherData.value.slice(currentLength, nextEndIndex);

  displayedWeather.value = [...displayedWeather.value, ...nextChunk];

  event.target.complete();

  // Disable infinite scroll if all data is loaded
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
  padding-block: 1rem;
  display: flex;
  flex-direction: row;
  align-items: flex-start;
  justify-content: space-between;
}

strong {
  font-weight: 900;
  color: var(--ion-text-color-secondary);
}
</style>