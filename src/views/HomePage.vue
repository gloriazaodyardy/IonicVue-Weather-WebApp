<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Weather Application</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Weather Application</ion-title>
        </ion-toolbar>
      </ion-header>

      <div id="container">
        <div>
          <h1>Welcome to the Weather Application!</h1>
          <p>
            This application provides you with the latest weather updates.
            Scroll to load more.
          </p>
          <!-- Search component -->
          <div>
            <SearchList @search-query="handleSearch"/>
          </div>
        </div>
        <!-- List component -->
        <div>
          <List :filterQuery="searchData"/>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
} from "@ionic/vue";
import { ref } from "vue";
import List from "@/components/List.vue";
import SearchList from "@/components/SearchList.vue";

const searchData = ref("");

const handleSearch = (query:string) =>{
  // save search query to searchQuery variable
  searchData.value = query;
}
</script>

<style scoped>
#container {
  margin-top: 28dvh;
  text-align: center;
  padding-inline: clamp(1rem, 4.286vw + 0.143rem, 4rem);

  div {
    margin-top: 4rem;
  }
}
</style>
