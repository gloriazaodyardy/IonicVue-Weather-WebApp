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
          <strong>Welcome to the Weather Application!</strong>
          <p>
            This application provides you with the latest weather updates.
            Scroll to load more.
          </p>
        </div>
        <!-- Display list -->
        <div>
          <List />
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
import List from "@/components/List.vue";
</script>

<style scoped>
#container {
  margin-top: 28dvh;
  text-align: center;
  padding-inline: 4rem;

  div {
    margin-top: 6rem;
    strong {
      font-size: 2rem;
    }
    p {
      color: #8c8c8c;
      font-size: 1.2rem;
    }
  }
}
</style>
