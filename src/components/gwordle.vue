<template>
  <v-container class="amethysta">
    <v-row v-if="!complete">
      <v-spacer/>
      <v-col cols="12" lg="8">
        <v-text-field
          label="Guess"
          v-model="guessName"
          class="guess-field"
          placeholder="The name of the card"
          color="white"
          background-color="#e2bd9d"
          outlined
          flat
          @keydown.enter="guessButton"
        >
          <template v-slot:append-outer>
            <v-btn @click="guessButton" class="guess-button" color="white" depressed>
              <v-icon color="black">mdi-send</v-icon>
            </v-btn>
          </template>
        </v-text-field>
      </v-col>
      <v-spacer/>
    </v-row>
    <results v-if="complete" :sharetext="sharetext" :guesses="guesses" :answer="answer" />
    <v-row>
      <v-simple-table class="guess-table" dense>
        <template v-slot:default>
          <thead>
            <tr>
              <th class="cell-header">Card</th>
              <th class="cell-header">Artist</th>
              <th class="cell-header">Type</th>
              <th style="max-width: 70px" class="cell-header">Rarity</th>
              <th style="max-width: 70px" class="cell-header">Cost</th>
              <th style="max-width: 70px" class="cell-header">Power</th>
              <th style="width: 90px" class="cell-header">Number</th>
            </tr>
          </thead>
          <tbody>
            <guess v-for="guess in orderedGuesses" :key="guess.Name" :guess="guess" :answer="answer" :width="300"/>
            <tr v-if="guesses.length == 0">
              <td style="width: 130px"/>
              <td style="width: 100px"/>
              <td style="width: 112px"/>
              <td style="width: 50px"/>
              <td style="width: 45px"/>
              <td style="width: 50px"/>
              <td style="width: 90px"/>              
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </v-row>
  </v-container>
</template>
<script>
import guess from './guess.vue'
import results from './results.vue'

import { Cards } from '../js/grottobeasts'

export default {
  name: 'Gwordle',
  props: ['answer', 'sharetext'],
  components: {
    guess,
    results
  },
  data: () => ({
    guessName: '',
    guesses: [],
    complete: false
  }),
  computed: {
    orderedGuesses () {
      return [].concat(this.guesses).reverse();
    }
  },
  watch: {
  },
  methods: {
    guessButton: function () {
      this.guessByName(this.guessName);
    },
    guessByName: function(name) {
      let foundOptions = this.filterName(Cards, name);

      let foundCard = null;

      if (foundOptions.length == 1) {
        foundCard = foundOptions[0];
      } else if (foundOptions.length > 0) {
        foundCard = this.exactName(foundOptions, name);
      }

      if (foundCard != null) {
        let existingGuess = this.guesses.find(g => g._id == foundCard._id);

        if (existingGuess == null) {
          this.submitGuess(foundCard);
          this.guessName = ''
        }
      }
    },
    submitGuess: function (guess) {
      this.guesses.push(guess);

      if (this.answer.Name == guess.Name) {
        // win
        this.win();
      }
    },
    win: function (){
      this.complete = true;
    },
    filterName: function(cards, name) {
      return cards.filter(c => c.Name.toLowerCase().includes(name.toLowerCase()));
    },
    exactName: function(cards, name) {
      return cards.find(c => c.Name.toLowerCase() == name.toLowerCase());
    }
  },
  mounted () {
  }
}
</script>
<style lang="scss">
.guess-field fieldset {
  border-color: white !important;
  border-width: 2px !important;
}
.guess-button {
  margin-top: -18px;
  margin-bottom: -18px;
  height: 56px !important;
}
.guess-table {
  background-color: inherit !important;
  width: 100%;
}

.guess-table th {
  color: white !important;
  border-bottom-width: 1px !important;
  border-bottom-color: white !important;
}
.guess-table tr:not(.won):hover {
  background-color: transparent !important;
}
.cell-header {
  font-weight: 100 !important;
  padding: 0px 7px !important;
}
.win-message {
  padding-bottom: 16px;
}
.answer-name {
  font-size: 3em;
}
.share-button {
  font-family: Roboto, sans-serif;
}

.image-credit {
  font-size: 1em;
}
</style>
