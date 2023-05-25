<template>
  <v-sheet rounded class="base">
    <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        density="compact"
      ></v-text-field>
    <v-data-table
      v-model:sort-by="sortBy"
      :headers="headers"
      :items="games"
      :search="search"
      class="elevation-1"
      multisort
    >
    <template v-slot:item.first="{ item }">
      <span :class="item.columns.first ? 'success' : 'error'">{{ item.columns.first ? 'First' : 'Second' }}</span>
    </template>
    <template v-slot:item.win="{ item }">
      <span :class="item.columns.win ? 'success' : 'error'">{{ item.columns.win ? 'Win' : 'Loss' }}</span>
    </template>
    <template v-slot:item.date="{ item }">
      {{ item.columns.date != null ? item.columns.date.replace( 'T', ' ').split('.')[0] : '' }}
    </template>
    </v-data-table>
  </v-sheet>
</template>

<script>
  import { VDataTable } from 'vuetify/labs/VDataTable'
  export default {
    components: { VDataTable },
    props: ['games'],
    data () {
      return {
        sortBy: [{ key: 'player', order: 'asc'}, { key: 'date', order: 'desc' }],
        headers: [
          { title: 'Player', key: 'player' },
          { title: 'Opponent', key: 'opponent' },
          { title: 'Deck', key: 'deckName' },
          { title: 'Format', key: 'gameFormat' },
          { title: 'Set', key: 'set' },
          { title: 'Commander', key: 'commander' },
          { title: 'Opposing Commander', key: 'opposingCommander' },
          { title: 'Mulls', key: 'mulls' },
          { title: 'Opponent Mulls', key: 'opponentMulls' },
          { title: 'Notes', key: 'notes' },
          { title: 'Went First', key: 'first' },
          { title: 'Won', key: 'win' },
          { title: 'Date', key: 'date' },
        ],
        search: ''
      }
    }
  }
</script>

<style scoped>
  .base {
    margin: 2em;
    padding: 2em;
  }

  .error {
    color: #b00020;
  }

  .success {
    color: #4caf4f;
  }
</style>