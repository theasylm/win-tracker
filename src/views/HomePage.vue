<template>
  <ion-page>
    <ion-content>
      <h1 class="header">MTGA Game Log</h1>
      <GameInputComponent @clicked="saveGame">
      </GameInputComponent>
      <GameTableComponent :games="games"></GameTableComponent>
    </ion-content>
  </ion-page>
</template>

<script>
  import { IonPage, IonContent } from '@ionic/vue';
  import GameInputComponent from '../components/GameInputComponent.vue'
  import GameTableComponent from '../components/GameTableComponent.vue'

  export default {
    components: {
      GameInputComponent,
      GameTableComponent,
      IonPage,
      IonContent
    },
    data() {
      return {
        games: []
      }
    },
    methods: {
      saveGame(game) {
        this.games.push(game)
      }
    },
    mounted() {
      fetch('http://localhost:3000/games.json', {
        method: 'GET',
        headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
        .then((response) => response.json())
        .then((json) => this.games = json)
    }

  }
</script>

<style scoped>
  .header {
    margin-left: 2em;
  }
</style>
