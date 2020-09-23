<template>
  <div class="dealer">
    <div class="container">
      <div class="card" v-for="card in firstCard" :key="card.texture">
        <img v-if="!firstCardVisible" src="../assets/cards/back.png" />
        <img v-if="firstCardVisible" :src="require(`../assets/cards/${card.texture}.png`)" />
      </div>
      <div class="card" v-for="card in hand" :key="card.texture">
        <img :src="require(`../assets/cards/${card.texture}.png`)" />
      </div>
    </div>
  </div>
</template>

<script>
import EventBus from "./event-bus";

let audio = new Audio(require("../assets/sound/deal.wav"));

const getInitialData = () => {
  return {
    hand: [],
    firstCard: [],
    handValue: 0,
    cardsInHand: 2,
    acesInHand: 0,
    blackjack: false,
    deck: [],
    cardsDealt: 0,
    firstCardVisible: false,
  };
};

export default {
  name: "Dealer",
  data() {
    return getInitialData();
  },
  methods: {
    stand() {
      audio.play();

      this.hand.unshift(this.firstCard[0]);
      this.firstCard.shift();

      while (this.handValue <= 16) {
        this.hand.push(this.deck[0][this.cardsDealt]);
        this.handValue += this.hand[this.cardsInHand].value;

        // In blackjack, ace is equal to 11 or 1
        if (this.hand[this.cardsInHand].value == 11) this.acesInHand++;
        if (this.acesInHand > 0 && this.handValue > 21) {
          this.acesInHand -= 1;
          this.handValue -= 10;
        }

        this.cardsInHand++;
        this.cardsDealt++;
      }

      EventBus.$emit("dealerScore", {
        dealerScore: this.handValue,
        blackjack: this.blackjack,
      });
    },
    resetData() {
      Object.assign(this.$data, getInitialData());
    },
  },
  created() {
    // Get card deck from CardDeck.vue
    EventBus.$on("dealCards", (payload) => {
      audio.play();

      this.deck.push(payload);

      // Deal first card separately, because first dealer card is hidden
      this.firstCard.push(this.deck[0][1]);
      this.hand.push(this.deck[0][3]);
      this.handValue = this.firstCard[0].value + this.hand[0].value;

      // Detect ace in hand
      if (this.firstCard[0].value == 11 || this.hand[0].value == 11)
        this.acesInHand = 1;
      if (this.firstCard[0].value == 11 && this.hand[0].value == 11) {
        this.acesInHand = 2;
        this.handValue = 12;
      }

      // Detect blackjack
      if (this.handValue == 21) this.blackjack = true;
    });
    EventBus.$on("stand", (payload) => {
      this.cardsDealt = payload;
      this.stand();
    });

    EventBus.$on("resetData", () => {
      this.resetData();
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.container {
  display: flex;
  flex-wrap: wrap;
  .card {
    display: inline-block;
    margin: 10px;
    padding: 10px;
    background-color: #fff;
    border: 3px solid black;
    border-radius: 5%;
    img {
      height: 250px;
    }
  }
}
</style>