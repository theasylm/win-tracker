<template>
  <v-sheet rounded class="base">
    <v-form v-model="valid" ref="form">
      <v-container class="full">
        <h3>Add Game</h3>
        <v-row dense>
          <v-col cols="2" class="top-row-input">
            <v-combobox density="compact" v-model="gameFormat" label="Format or Event" :items="knownFormatsAndEvents" :rules="formatRules"></v-combobox>
          </v-col>
          <v-col cols="2" class="top-row-input">
            <v-combobox density="compact" v-model="player" label="Player" :items="knownPlayers" :rules="playerRules"></v-combobox>
          </v-col>
          <v-col cols="2" class="top-row-input">
            <v-combobox density="compact" v-model="commander" label="Commander" :items="knownCommanders" :rules="commanderRules"></v-combobox>
          </v-col>
          <v-col cols="1" class="top-row-input">
            <v-text-field
              v-model="mulls"
              type="number"
              density="compact"
              label="Mulls"
            />
          </v-col>
          <v-col cols="1">
            <v-radio-group v-model="first" inline>
              <v-radio label="First" value="true"></v-radio>
              <v-radio label="Second" value="false"></v-radio>
            </v-radio-group>
          </v-col>
          <v-col cols="1">
            <v-radio-group v-model="win" inline>
              <v-radio label="Win" value="true"></v-radio>
              <v-radio label="Loss" value="false"></v-radio>
            </v-radio-group>
          </v-col>
          <v-col cols="2" class="button-col">
            <v-btn color="success"
                  @click="saveGame"
                  :disabled="allBlank || !valid">
              Save Game
            </v-btn>
            <v-btn color="error"
                  @click="clear"
                  class="nudge-right">
              Clear
            </v-btn>
          </v-col>
        </v-row>
        <v-row dense>
          <v-col cols="2">
            <v-combobox density="compact" v-model="set" label="Draft Set" :items="knownSets" :rules="setRules"></v-combobox>
          </v-col>
          <v-col cols="2">
            <v-combobox density="compact" v-model="opponent" label="Opponent" :items="knownPlayers" :rules="opponentRules"></v-combobox>
          </v-col>
          <v-col cols="2">
            <v-combobox density="compact" v-model="opposingCommander" label="Opposing Commander" :items="knownCommanders" :rules="opposingCommanderRules"></v-combobox>
          </v-col>
          <v-col cols="1">
            <v-text-field
              v-model="opponentMulls"
              type="number"
              density="compact"
              label="Opponent Mulls"
            />
          </v-col>
          <v-col cols="2">
            <v-combobox label="Deck Name" v-model="deckName" density="compact" :items="knownDeckNames" :rules="deckNameRules"></v-combobox>
          </v-col>
          <v-col cols="2">
            <v-textarea label="Notes" v-model="notes" no-resize rows=1 density="compact"></v-textarea>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </v-sheet>
</template>

<script>
  export default {
    data() {
      return {
        player: '',
        opponent: '',
        gameFormat: '',
        set: '',
        win: 'true',
        deckName: '',
        commander: '',
        opposingCommander: '',
        mulls: 0,
        opponentMulls: 0,
        notes: '',
        first: 'true',
        valid: false,
        formatRules: [
          value => {
            if (value) return true

            if ( this.allBlank ) return true

            return 'You must select or enter a format.'
          },
        ],
        playerRules: [
          value => {
            if (value) return true

            if ( this.allBlank ) return true

            return 'You must select or enter a player name.'
          },
        ],
        opponentRules: [
          value => {
            if (value) return true

            if ( this.allBlank ) return true

            return 'You must select or enter an opponent.'
          },
        ],
        deckNameRules: [
          value => {
            if (value) return true

            if ( this.allBlank ) return true

            return 'You must select or enter a deck name.'
          },
        ],
        commanderRules: [
          value => {
            if ( this.gameFormat != 'Standard Brawl' && this.gameFormat != 'Historic Brawl' ){
              if ( value == '' ) {
                return true
              }
                
              return 'Do not enter a commander when not in Brawl.'
            }
              

            if (value) return true

            return 'You must select or enter a commander.'
          },
        ],
        opposingCommanderRules: [
          value => {
            if ( this.gameFormat != 'Standard Brawl' && this.gameFormat != 'Historic Brawl' ){
              if ( value == '' ) {
                return true
              }
                
              return 'Do not enter an opposing commander when not in Brawl.'
            }
              

            if (value) return true

            return 'You must select or enter an opposing commander.'
          },
        ],
        setRules: [
          value => {
            if ( this.gameFormat != 'Premier Draft' && this.gameFormat != 'Traditional Draft' && this.gameFormat != 'Quick Draft' ){
              if ( value == '' ) {
                return true
              }
                
              return 'Do not enter a set when not drafting.'
            }
              
            if (value == '') 
              return 'You must select or enter the set.'

            if ( /.+ - [A-Z]{3}/.test(value) )
              return true

            return 'You must format the set in "{{Set name}} - {{Set Code}}" format.'
          },
        ],
        knownPlayers: [],
        knownFormatsAndEvents: [],
        knownSets: [],
        knownCommanders: [],
        knownDeckNames: [],
      }
    },
    methods: {
      clear() {
        this.player = ''
        this.opponent = ''
        this.gameFormat = ''
        this.set = ''
        this.win = 'true'
        this.deckName = ''
        this.commander = ''
        this.opposingCommander = ''
        this.mulls = 0 
        this.opponentMulls = 0
        this.notes = ''
        this.first = 'true'
      },
      saveGame() {
       let game = {
          player: this.player,
          opponent: this.opponent,
          gameFormat: this.gameFormat,
          set: this.set,
          win: this.win == 'true',
          deckName: this.deckName,
          commander: this.commander,
          opposingCommander: this.opposingCommander,
          mulls: this.mulls,
          opponentMulls: this.opponentMulls,
          notes: this.notes,
          first: this.first == 'true',
          date: new Date().toISOString()
        }
        let params = JSON.stringify(game)
        fetch('http://localhost:3000/games/raw.json', {
          method: 'POST',
          body: params,
          headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
          .then((response) => response.json())
          .then((json) => console.log(json));
        
        this.checkKnownPlayers(game)
        this.checkKnownCommanders(game)
        this.checkKnownSets(game.set)
        this.checkKnownFormatsAndEvents(game.gameFormat)
        this.checkKnownDeckNames(game.deckName)

        this.$emit('clicked', game)
        this.clear()
      },
      validate: function() {
        this.$refs.form.validate();
      },
      checkKnownPlayers(game) {
        let sort = false
        if ( this.knownPlayers.filter(player => player == game.player).length == 0 ){
          this.knownPlayers.push(game.player)
          sort = true
        }
        if ( this.knownPlayers.filter(player => player == game.opponent).length == 0 ){
          this.knownPlayers.push(game.opponent)
          sort = true
        }
        if ( sort ) {
          this.knownPlayers = this.knownPlayers.sort()
        }
      },
      checkKnownCommanders(game) {
        let sort = false
        if ( game.commander != '' && this.knownCommanders.filter(commander => commander == game.commander).length == 0 ){
          this.knownCommanders.push(game.commander)
          sort = true
        }
        if ( game.opposingCommander != '' && this.knownCommanders.filter(commander => commander == game.oppoingCommander).length == 0 ){
          this.knownCommanders.push(game.opposingCommander)
          sort = true
        }
        if ( sort ) {
          this.knownCommanders = this.knownCommanders.sort()
        }
      },
      checkKnownSets(newSet) {
        if ( newSet == '' ) {
          return
        }
        if ( this.knownSets.filter(set => set == newSet).length == 0 ) {
          this.knownSets.push(newSet)
          this.knownSets = this.knownSets.sort()
        }
      },
      checkKnownFormatsAndEvents(newFormat) {
        if ( this.knownFormatsAndEvents.filter(format => format == newFormat).length == 0 ) {
          this.knownFormatsAndEvents.push(newFormat)
        }
      },
      checkKnownDeckNames(newDeckName) {
        if ( this.knownDeckNames.filter(deckName => deckName == newDeckName).length == 0) {
          this.knownDeckNames.push(newDeckName)
          this.knownDeckNames = this.knownDeckNames.sort()
        }
      }
    },
    watch: {
      gameFormat: 'validate',
      commander: 'validate',
      opposingCommander: 'validate',
      set: 'validate',
      mulls: 'validate',
      opponentMulls: 'validate',
      notes: 'validate',
      player: 'validate',
      opponent: 'validate',
      win: 'validate',
      deckName: 'validate',
      first: 'validate'
    },
    computed: {
      allBlank() {
        if ( this.player == '' && this.opponent == '' && this.gameFormat == '' && this.set == '' && this.deckName == '' &&
             this.win == 'true' && this.commander == '' && this.opposingCommander == '' && this.mulls == 0 &&
             this.opponentMulls == 0 && this.notes == '' && this.first == 'true')
          return true
        
        return false
      }
    },
    mounted() {
      fetch('http://localhost:3000/formats.json', {
        method: 'GET',
        headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
        .then((response) => response.json())
        .then((json) => this.knownFormatsAndEvents = json.map(o => o.name))

        fetch('http://localhost:3000/players.json', {
        method: 'GET',
        headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
        .then((response) => response.json())
        .then((json) => this.knownPlayers = json.map(o => o.name))

        fetch('http://localhost:3000/commanders.json', {
        method: 'GET',
        headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
        .then((response) => response.json())
        .then((json) => this.knownCommanders = json.map(o => o.name))

        fetch('http://localhost:3000/magic_sets.json', {
        method: 'GET',
        headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
        .then((response) => response.json())
        .then((json) => this.knownSets = json.map(o => o.name))

        fetch('http://localhost:3000/decks.json', {
        method: 'GET',
        headers: {
            "Content-type": "application/json; charset=UTF-8"
          }
        })
        .then((response) => response.json())
        .then((json) => this.knownDeckNames = json.map(o => o.name))
    }
  }
</script>

<style scoped>
  .base {
    padding: 0 2em;
    margin: 0 2em;
  }
  .full {
    max-width: 100%;
  }

  .top-row-input {
    margin-top: .5em;
  }
  .nudge-right {
    margin-left: .25em;
  }
  .button-col {
    margin-top: 1.25em;
  }
</style>