<template>
  <div class="game d-flex flex-column align-items-center">
    <h1 class="game__title pb-3 pt-4">Memory Game</h1>
    <div v-if="!name" class="name-container">
      <input type="text" v-model="playerName" class="name-container__input form-control mb-2" placeholder="Ingresa tu nombre">
      <button @click="startGame" class="name-container__button btn btn-primary">Inicar Juego</button>
    </div>
    <div v-else class="board">
      <div class="row">
        <div v-for="(card, index) in shuffledCards" :key="index" class="col-3 col-md-4 col-lg-2 col-xl-2 mb-4 d-flex justify-content-center">
          <MemoryCard :image="card.image" :flipped="card.flipped" :found="card.found" @click="flipCard(index)"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import MemoryCard from './components/MemoryCard.vue'

export default {
  name: 'App',
  components: {
    MemoryCard
  },
  data () {
    return {
      playerName: '',
      name: '',
      cards: [],
      flippedCards: []
    }
  },
  computed: {
    shuffledCards() {
      const sortedCards = this.cards.slice(-24)
      return sortedCards.sort(() => Math.random() - 0.5)
    }
  },
  methods: {
    startGame(){
      this.name = this.playerName
      this.getCards()
    },
    getCards() {
      const endpoint = 'https://fed-team.modyo.cloud/api/content/spaces/animals/types/game/entries?per_page=20'
      axios.get(endpoint)
        .then((response) =>{
          const apiData = response.data.entries

          const memoryCards = apiData.flatMap((animal) => [
              { id: animal.meta.uuid + '_1', name: animal.meta.name, image: animal.fields.image.url, flipped: false, found: false },
              { id: animal.meta.uuid + '_2', name: animal.meta.name, image: animal.fields.image.url, flipped: false, found: false }
            ])
          
          this.cards = memoryCards
          console.log('memory cards', memoryCards)
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },
    flipCard(index) {
      console.log('Flip card index:', index)
      console.log('Is it flipped?', this.cards[index].flipped)
      if (!this.cards[index].flipped && this.flippedCards.length < 2) {
        // Flip the card
        this.cards[index].flipped = true;
        this.flippedCards.push(index);

        // Check for a match if two cards are flipped
        if (this.flippedCards.length === 2) {
          const [cardIndex1, cardIndex2] = this.flippedCards;
          if (this.cards[cardIndex1].name === this.cards[cardIndex2].name) {
            // Cards match
            this.cards[cardIndex1].found = true;
            this.cards[cardIndex2].found = true;
          } else {
            // Cards don't match, flip back after a short delay
            setTimeout(() => {
              this.cards[cardIndex1].flipped = false;
              this.cards[cardIndex2].flipped = false;
            }, 1000);
          }
          this.flippedCards = []; // Clear flipped cards for the next turn
        }
      }
    },
  }
}
</script>
<style lang="sss" scoped>

</style>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100vh;
  width: 100vw;
}
.name-container{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 80vh;
}
.name-container__input{
  max-width: 250px;
  margin: 0 auto;
}
.board {
  width: 82%;
  max-width: 850px;
}
@media (min-width: 1200px) {
  .board {
    width: 55%
  }
}
@media (max-width: 768px) {
  .board {
    width: 90%
  }
}
</style>
