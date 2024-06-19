<template>
  <main class="flex justify-center h-[100vh]">
    <div class="h-full">
      <div>
        <button @click="startGame">Start!</button>
      </div>
      <div class="flex items-center gap-16 h-full">
        <div class="flex flex-col justify-between items-center gap-24 h-full">
          <div>
            <p>Player 1:</p>
            <div class="flex flex-wrap gap-2">
              <Card v-for="card in playerOneHand" :key="card.id" :card="card" />
            </div>
          </div>
          <div class="w-full h-full flex gap-2">
            <Card v-for="card in fieldCards" :key="card.id" :card="card" />
          </div>
          <div>
            <p>Player 2:</p>
            <div class="flex flex-wrap gap-2">

              <Card v-for="card in playerTwoHand" :key="card.id" :card="card" @click="pickForTurn(card)"
                class="cursor-pointer" :class="{ 'translate-y-[-12px]': pickedForTurnCards.includes(card) }" />

            </div>
          </div>
        </div>
        <div v-if="gameStarted">
          <Card :card="trumpCard" />
        </div>
      </div>
    </div>
    <div>
      <button @click="makeTurn">Make Turn</button>
    </div>
  </main>
</template>

<script setup>
import Card from '../components/Card.vue'
import { ref } from 'vue';

const gameStarted = ref(false)

const deck = ref([
  { "id": 1, "suit": "hearts", "rank": "6", "value": 6, "image": "/images/cards/H6.png" },
  { "id": 2, "suit": "hearts", "rank": "7", "value": 7, "image": "/images/cards/H7.png" },
  { "id": 3, "suit": "hearts", "rank": "8", "value": 8, "image": "/images/cards/H8.png" },
  { "id": 4, "suit": "hearts", "rank": "9", "value": 9, "image": "/images/cards/H9.png" },
  { "id": 5, "suit": "hearts", "rank": "10", "value": 10, "image": "/images/cards/H10.png" },
  { "id": 6, "suit": "hearts", "rank": "J", "value": 11, "image": "/images/cards/HJ.png" },
  { "id": 7, "suit": "hearts", "rank": "Q", "value": 12, "image": "/images/cards/HQ.png" },
  { "id": 8, "suit": "hearts", "rank": "K", "value": 13, "image": "/images/cards/HK.png" },
  { "id": 9, "suit": "hearts", "rank": "A", "value": 14, "image": "/images/cards/HA.png" },

  { "id": 10, "suit": "diamonds", "rank": "6", "value": 6, "image": "/images/cards/D6.png" },
  { "id": 11, "suit": "diamonds", "rank": "7", "value": 7, "image": "/images/cards/D7.png" },
  { "id": 12, "suit": "diamonds", "rank": "8", "value": 8, "image": "/images/cards/D8.png" },
  { "id": 13, "suit": "diamonds", "rank": "9", "value": 9, "image": "/images/cards/D9.png" },
  { "id": 14, "suit": "diamonds", "rank": "10", "value": 10, "image": "/images/cards/D10.png" },
  { "id": 15, "suit": "diamonds", "rank": "J", "value": 11, "image": "/images/cards/DJ.png" },
  { "id": 16, "suit": "diamonds", "rank": "Q", "value": 12, "image": "/images/cards/DQ.png" },
  { "id": 17, "suit": "diamonds", "rank": "K", "value": 13, "image": "/images/cards/DK.png" },
  { "id": 18, "suit": "diamonds", "rank": "A", "value": 14, "image": "/images/cards/DA.png" },

  { "id": 19, "suit": "clubs", "rank": "6", "value": 6, "image": "/images/cards/C6.png" },
  { "id": 20, "suit": "clubs", "rank": "7", "value": 7, "image": "/images/cards/C7.png" },
  { "id": 21, "suit": "clubs", "rank": "8", "value": 8, "image": "/images/cards/C8.png" },
  { "id": 22, "suit": "clubs", "rank": "9", "value": 9, "image": "/images/cards/C9.png" },
  { "id": 23, "suit": "clubs", "rank": "10", "value": 10, "image": "/images/cards/C10.png" },
  { "id": 24, "suit": "clubs", "rank": "J", "value": 11, "image": "/images/cards/CJ.png" },
  { "id": 25, "suit": "clubs", "rank": "Q", "value": 12, "image": "/images/cards/CQ.png" },
  { "id": 26, "suit": "clubs", "rank": "K", "value": 13, "image": "/images/cards/CK.png" },
  { "id": 27, "suit": "clubs", "rank": "A", "value": 14, "image": "/images/cards/CA.png" },

  { "id": 28, "suit": "spades", "rank": "6", "value": 6, "image": "/images/cards/S6.png" },
  { "id": 29, "suit": "spades", "rank": "7", "value": 7, "image": "/images/cards/S7.png" },
  { "id": 30, "suit": "spades", "rank": "8", "value": 8, "image": "/images/cards/S8.png" },
  { "id": 31, "suit": "spades", "rank": "9", "value": 9, "image": "/images/cards/S9.png" },
  { "id": 32, "suit": "spades", "rank": "10", "value": 10, "image": "/images/cards/S10.png" },
  { "id": 33, "suit": "spades", "rank": "J", "value": 11, "image": "/images/cards/SJ.png" },
  { "id": 34, "suit": "spades", "rank": "Q", "value": 12, "image": "/images/cards/SQ.png" },
  { "id": 35, "suit": "spades", "rank": "K", "value": 13, "image": "/images/cards/SK.png" },
  { "id": 36, "suit": "spades", "rank": "A", "value": 14, "image": "/images/cards/SA.png" }
])

const trumpCard = ref({})

const playerOneHand = ref([])
const playerTwoHand = ref([])

const dealplayerOneHand = () => {
  const hand = []
  do {
    const cardIdx = Math.floor(Math.random() * deck.value.length);
    const card = deck.value[cardIdx]
    hand.push(card)
    deck.value.splice(cardIdx, 1)
  } while (hand.length < 6);

  return playerOneHand.value = hand
}

const dealplayerTwoHand = () => {
  const hand = []
  do {
    const cardIdx = Math.floor(Math.random() * deck.value.length);
    const card = deck.value[cardIdx]
    hand.push(card)
    deck.value.splice(cardIdx, 1)
  } while (hand.length < 6);

  return playerTwoHand.value = hand
}

const setTrump = () => {
  const cardIdx = Math.floor(Math.random() * deck.value.length);
  if (deck.value[cardIdx].value === 14) {
    setTrump()
  }
  else return trumpCard.value = deck.value[cardIdx]
}

const setDefaultDeck = () => {
  deck.value = [
    { "id": 1, "suit": "hearts", "rank": "6", "value": 6, "image": "/images/cards/H6.png" },
    { "id": 2, "suit": "hearts", "rank": "7", "value": 7, "image": "/images/cards/H7.png" },
    { "id": 3, "suit": "hearts", "rank": "8", "value": 8, "image": "/images/cards/H8.png" },
    { "id": 4, "suit": "hearts", "rank": "9", "value": 9, "image": "/images/cards/H9.png" },
    { "id": 5, "suit": "hearts", "rank": "10", "value": 10, "image": "/images/cards/H10.png" },
    { "id": 6, "suit": "hearts", "rank": "J", "value": 11, "image": "/images/cards/HJ.png" },
    { "id": 7, "suit": "hearts", "rank": "Q", "value": 12, "image": "/images/cards/HQ.png" },
    { "id": 8, "suit": "hearts", "rank": "K", "value": 13, "image": "/images/cards/HK.png" },
    { "id": 9, "suit": "hearts", "rank": "A", "value": 14, "image": "/images/cards/HA.png" },

    { "id": 10, "suit": "diamonds", "rank": "6", "value": 6, "image": "/images/cards/D6.png" },
    { "id": 11, "suit": "diamonds", "rank": "7", "value": 7, "image": "/images/cards/D7.png" },
    { "id": 12, "suit": "diamonds", "rank": "8", "value": 8, "image": "/images/cards/D8.png" },
    { "id": 13, "suit": "diamonds", "rank": "9", "value": 9, "image": "/images/cards/D9.png" },
    { "id": 14, "suit": "diamonds", "rank": "10", "value": 10, "image": "/images/cards/D10.png" },
    { "id": 15, "suit": "diamonds", "rank": "J", "value": 11, "image": "/images/cards/DJ.png" },
    { "id": 16, "suit": "diamonds", "rank": "Q", "value": 12, "image": "/images/cards/DQ.png" },
    { "id": 17, "suit": "diamonds", "rank": "K", "value": 13, "image": "/images/cards/DK.png" },
    { "id": 18, "suit": "diamonds", "rank": "A", "value": 14, "image": "/images/cards/DA.png" },

    { "id": 19, "suit": "clubs", "rank": "6", "value": 6, "image": "/images/cards/C6.png" },
    { "id": 20, "suit": "clubs", "rank": "7", "value": 7, "image": "/images/cards/C7.png" },
    { "id": 21, "suit": "clubs", "rank": "8", "value": 8, "image": "/images/cards/C8.png" },
    { "id": 22, "suit": "clubs", "rank": "9", "value": 9, "image": "/images/cards/C9.png" },
    { "id": 23, "suit": "clubs", "rank": "10", "value": 10, "image": "/images/cards/C10.png" },
    { "id": 24, "suit": "clubs", "rank": "J", "value": 11, "image": "/images/cards/CJ.png" },
    { "id": 25, "suit": "clubs", "rank": "Q", "value": 12, "image": "/images/cards/CQ.png" },
    { "id": 26, "suit": "clubs", "rank": "K", "value": 13, "image": "/images/cards/CK.png" },
    { "id": 27, "suit": "clubs", "rank": "A", "value": 14, "image": "/images/cards/CA.png" },

    { "id": 28, "suit": "spades", "rank": "6", "value": 6, "image": "/images/cards/S6.png" },
    { "id": 29, "suit": "spades", "rank": "7", "value": 7, "image": "/images/cards/S7.png" },
    { "id": 30, "suit": "spades", "rank": "8", "value": 8, "image": "/images/cards/S8.png" },
    { "id": 31, "suit": "spades", "rank": "9", "value": 9, "image": "/images/cards/S9.png" },
    { "id": 32, "suit": "spades", "rank": "10", "value": 10, "image": "/images/cards/S10.png" },
    { "id": 33, "suit": "spades", "rank": "J", "value": 11, "image": "/images/cards/SJ.png" },
    { "id": 34, "suit": "spades", "rank": "Q", "value": 12, "image": "/images/cards/SQ.png" },
    { "id": 35, "suit": "spades", "rank": "K", "value": 13, "image": "/images/cards/SK.png" },
    { "id": 36, "suit": "spades", "rank": "A", "value": 14, "image": "/images/cards/SA.png" }
  ]
}

const startGame = () => {
  setDefaultDeck()
  dealplayerOneHand()
  dealplayerTwoHand()
  setTrump()
  gameStarted.value = true
}

const fieldCards = ref([])

const pickedForTurnCards = ref([])

const pickForTurn = (card) => {
  if (pickedForTurnCards.value.length > 0) {
    if (pickedForTurnCards.value.includes(card)) {
      const cardIdx = pickedForTurnCards.value.indexOf(card);
      pickedForTurnCards.value.splice(cardIdx, 1);

    }
    else if (!pickedForTurnCards.value.find(pickedCard => pickedCard.value === card.value)) {
      return
    } else pickedForTurnCards.value.push(card)
  }
  else pickedForTurnCards.value.push(card)
}

const makeTurn = () => {
  fieldCards.value = pickedForTurnCards.value
}

</script>