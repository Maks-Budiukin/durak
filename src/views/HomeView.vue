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
            <div class="flex items-center gap-2 text-white my-4">
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="playerOneAttackRound ? makeAttackTurn() : null">Make Turn</button>
              </div>
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="!playerOneAttackPhase && playerOneTurn ? makeDefenceTurn() : null">Make Defence Turn</button>
              </div>
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="playerOneAttackRound ? endRound() : null">Beaten</button>
              </div>
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="!playerOneAttackPhase && playerOneTurn ? grabCards() : null">Grab</button>
              </div>
            </div>
            <div class="flex flex-wrap gap-2">

              <Card v-for="card in playerOneHand" :key="card.id" :card="card"
                @click="playerOneAttackRound ? pickForTurn(card) : pickForDefence(card)" class="cursor-pointer"
                :class="{ 'translate-y-[-12px]': pickedForDefenceCards.includes(card), 'translate-y-[-12px]': pickedForTurnCards.includes(card) }" />

            </div>
          </div>
          <div class="w-full relative">
            <div class="w-full flex gap-2">
              <Card v-for="card in fieldAttackCards" :key="card.id" :card="card" />
            </div>

            <div class="w-full flex gap-2 absolute top-16 left-20">
              <Card v-for="card in fieldDefenceCards" :key="card.id" :card="card" />
            </div>
          </div>

          <div>
            <p>Player 2:</p>
            <div class="flex items-center gap-2 text-white my-4">
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="playerTwoAttackRound ? makeAttackTurn() : null">Make Turn</button>
              </div>
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="playerOneAttackPhase && !playerOneTurn ? makeDefenceTurn() : null">Make Defence Turn</button>
              </div>
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="playerTwoAttackRound ? endRound() : null">Beaten</button>
              </div>
              <div>
                <button class="px-4 py-2 bg-slate-400 hover:bg-slate-500 duration-300 rounded border border-[#64748b]"
                  @click="playerOneAttackPhase && !playerOneTurn ? grabCards() : null">Grab</button>
              </div>
            </div>
            <div class="flex flex-wrap gap-2">

              <Card v-for="card in playerTwoHand" :key="card.id" :card="card"
                @click="playerTwoAttackRound ? pickForTurn(card) : pickForDefence(card)" class="cursor-pointer"
                :class="{ 'translate-y-[-12px]': pickedForDefenceCards.includes(card), 'translate-y-[-12px]': pickedForTurnCards.includes(card) }" />

            </div>
          </div>
        </div>
        <div v-if="gameStarted">
          <Card :card="trumpCard" />
        </div>
      </div>
    </div>
    <!-- <div class="flex gap-2">
      <div>
        <button @click="makeAttackTurn">Make Turn</button>
      </div>
      <div>
        <button @click="makeDefenceTurn">Make Defence Turn</button>
      </div>
      <div>
        <button @click="endRound">Beaten</button>
      </div>
      <div>
        <button @click="grabCards">Grab</button>
      </div>
    </div> -->
  </main>
</template>

<script setup>
import Card from '../components/Card.vue'
import { ref, computed } from 'vue';

const gameStarted = ref(false)

const playerOneTurn = ref(true)

const playerOneAttackPhase = ref(true)
// const playerTwoAttackPhase = ref(false)

const playerOneAttackRound = computed(() => {
  return playerOneAttackPhase.value && playerOneTurn.value
})

const playerTwoAttackRound = computed(() => {
  return !playerOneAttackPhase.value && !playerOneTurn.value
})

const trumpCard = ref({})

const trumpOnTable = ref(true)

const playerOneHand = ref([])
const playerTwoHand = ref([])

const fieldCards = computed(() => {
  return [...fieldAttackCards.value, ...fieldDefenceCards.value]
})

const fieldAttackCards = ref([])

const fieldDefenceCards = ref([])

const attackCards = ref([])
const defenceCards = ref([])

const pickedForTurnCards = ref([])
const pickedForDefenceCards = ref([])

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

const dealplayerOneHand = () => {
  const hand = playerOneHand.value
  if (hand.length < 6) {
    do {
      const cardIdx = Math.floor(Math.random() * deck.value.length);
      const card = deck.value[cardIdx]
      if (deck.value.length > 0) {
        hand.push(card)
        deck.value.splice(cardIdx, 1)
      }
      else {
        if (trumpOnTable.value) {
          hand.push(trumpCard.value)
          trumpOnTable.value = false
        }
      }
    } while (hand.length < 6 && deck.value.length > 0);
  }

  return playerOneHand.value = hand
}

const dealplayerTwoHand = () => {
  const hand = playerTwoHand.value
  if (hand.length < 6) {
    do {
      const cardIdx = Math.floor(Math.random() * deck.value.length);
      const card = deck.value[cardIdx]
      if (deck.value.length > 0) {
        hand.push(card)
        deck.value.splice(cardIdx, 1)
      }
      else {
        if (trumpOnTable.value) {
          hand.push(trumpCard.value)
          trumpOnTable.value = false
        }
      }
    } while (hand.length < 6 && deck.value.length > 0);
  }

  return playerTwoHand.value = hand
}

const setTrump = () => {
  const cardIdx = Math.floor(Math.random() * deck.value.length);
  if (deck.value[cardIdx].value === 14) {
    setTrump()
  }
  else {
    trumpCard.value = deck.value[cardIdx]
    deck.value.splice(deck.value.indexOf(trumpCard.value), 1)
  }
}

const startGame = () => {
  setDefaultDeck()
  pickedForTurnCards.value = []
  // fieldCards.value = []
  fieldAttackCards.value = []
  fieldDefenceCards.value = []
  setTrump()
  deck.value.map(card => {
    if (card.suit === trumpCard.value.suit) {
      card.value = card.value * 10
    }
  })
  trumpCard.value.value = trumpCard.value.value * 10
  dealplayerOneHand()
  dealplayerTwoHand()
  gameStarted.value = true
}

const pickForTurn = (card) => {

  if (fieldCards.value.length === 0) {

    if (pickedForTurnCards.value.length > 0) {

      if (pickedForTurnCards.value.includes(card)) {
        const cardIdx = pickedForTurnCards.value.indexOf(card);
        pickedForTurnCards.value.splice(cardIdx, 1);

      }

      else if (pickedForTurnCards.value.find(pickedCard => pickedCard.rank === card.rank)) {
        pickedForTurnCards.value.push(card)
      } else
        return
    }

    else pickedForTurnCards.value.push(card)
  }

  else {
    const availableRanks = []
    fieldCards.value.map(card => {
      availableRanks.push(card.rank)
    })

    if (pickedForTurnCards.value.includes(card)) {
      const cardIdx = pickedForTurnCards.value.indexOf(card);
      pickedForTurnCards.value.splice(cardIdx, 1);

    }
    else {
      if (availableRanks.includes(card.rank)) {
        pickedForTurnCards.value.push(card)
      } else
        return
    }
  }
}

const makeAttackTurn = () => {
  attackCards.value.push(...pickedForTurnCards.value)
  fieldAttackCards.value.push(...pickedForTurnCards.value)
  // fieldCards.value.push(...pickedForTurnCards.value)

  if (playerOneTurn.value) {
    pickedForTurnCards.value.map(card => playerOneHand.value.splice(playerOneHand.value.indexOf(card), 1))
  } else {
    pickedForTurnCards.value.map(card => playerTwoHand.value.splice(playerTwoHand.value.indexOf(card), 1))
  }

  pickedForTurnCards.value = []
  endTurn()
}

const pickForDefence = (card) => {

  if (pickedForDefenceCards.value.includes(card)) {
    const cardIdx = pickedForDefenceCards.value.indexOf(card);
    pickedForDefenceCards.value.splice(cardIdx, 1);

  }
  else pickedForDefenceCards.value.push(card)

}

const getBeatingSet = (attackCards, defenceCards) => {
  const beatingSet = []

  attackCards.map(attackCard => {

    const possibleBeatingCards = []

    defenceCards.map(defenceCard => {
      if ((defenceCard.suit === attackCard.suit || defenceCard.suit === trumpCard.value.suit) && defenceCard.value > attackCard.value) {
        possibleBeatingCards.push(defenceCard)
      }
    })

    if (possibleBeatingCards.length > 0) {
      const lowestBeatingCard = possibleBeatingCards.reduce((minCard, currentCard) => {
        return currentCard.value < minCard.value ? currentCard : minCard;
      }, possibleBeatingCards[0]);

      beatingSet.push(lowestBeatingCard)
      defenceCards.splice(defenceCards.indexOf(lowestBeatingCard), 1)
    }
    else
      console.log('not enough power!')
    return

  })

  if (attackCards.length === beatingSet.length) {
    return beatingSet
  }

  else return false
}


const endTurn = () => {
  playerOneTurn.value = !playerOneTurn.value
}

const makeDefenceTurn = () => {
  const beatingSet = getBeatingSet(attackCards.value, pickedForDefenceCards.value)
  if (beatingSet) {
    fieldDefenceCards.value.push(...beatingSet)
    if (playerOneTurn.value) {
      beatingSet.map(card => playerOneHand.value.splice(playerOneHand.value.indexOf(card), 1))
    } else {
      beatingSet.map(card => playerTwoHand.value.splice(playerTwoHand.value.indexOf(card), 1))
    }
    pickedForDefenceCards.value = []
    attackCards.value = []
    defenceCards.value = []
    endTurn()
  }
  else {
    console.log('Can\'t beat! :\'(')
  }
}

const winningAward = () => {
  if (playerOneHand.value.length === 0 && deck.value.length === 0) {
    alert('player one wins!')
  }

  else if (playerTwoHand.value.length === 0 && deck.value.length === 0) {
    alert('player two wins!')
  }

  else return
}

const endRound = () => {
  endTurn()
  playerOneAttackPhase.value = !playerOneAttackPhase.value
  fieldAttackCards.value = []
  fieldDefenceCards.value = []
  attackCards.value = []
  defenceCards.value = []

  dealplayerOneHand()
  dealplayerTwoHand()

  winningAward()
}

const grabCards = () => {
  endTurn()

  if (playerOneAttackPhase.value && !playerOneTurn.value) {
    playerTwoHand.value.push(...fieldCards.value)
  } else if (!playerOneAttackPhase.value && playerOneTurn.value) {
    playerOneHand.value.push(...fieldCards.value)
  }

  fieldAttackCards.value = []
  fieldDefenceCards.value = []
  attackCards.value = []
  defenceCards.value = []

  dealplayerOneHand()
  // dealplayerTwoHand()

  winningAward()
}



</script>