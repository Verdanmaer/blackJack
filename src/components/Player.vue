<template>
  <div class="player">
    <div class="player__cards">
      <transition-group name="fade">
        <div v-for="card in hand" :key="card.texture" class="card">
          <img :src="require(`../assets/cards/${card.texture}.png`)" />
        </div>
      </transition-group>
      <div class="player__hand-value">
        {{ handValue }}
      </div>
    </div>

    <div class="player__controls">
        <button class="player__controls--stand" @click="stand">STAND</button>
        <div class="bank">
          <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" fill="currentColor" class="bi bi-coin" viewBox="0 0 16 16">
            <path d="M5.5 9.511c.076.954.83 1.697 2.182 1.785V12h.6v-.709c1.4-.098 2.218-.846 2.218-1.932 0-.987-.626-1.496-1.745-1.76l-.473-.112V5.57c.6.068.982.396 1.074.85h1.052c-.076-.919-.864-1.638-2.126-1.716V4h-.6v.719c-1.195.117-2.01.836-2.01 1.853 0 .9.606 1.472 1.613 1.707l.397.098v2.034c-.615-.093-1.022-.43-1.114-.9H5.5zm2.177-2.166c-.59-.137-.91-.416-.91-.836 0-.47.345-.822.915-.925v1.76h-.005zm.692 1.193c.717.166 1.048.435 1.048.91 0 .542-.412.914-1.135.982V8.518l.087.02z"/>
            <path d="M8 15A7 7 0 1 1 8 1a7 7 0 0 1 0 14zm0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16z"/>
            <path d="M8 13.5a5.5 5.5 0 1 1 0-11 5.5 5.5 0 0 1 0 11zm0 .5A6 6 0 1 0 8 2a6 6 0 0 0 0 12z"/>
          </svg>
          <small>x</small>
          <span>{{ money }}</span>
        </div>
        <button class="player__controls--hit" @click="hit">HIT</button>
    </div>
  </div>
</template>

<script>
import EventBus from "../event-bus";

let audio = new Audio(require("../assets/sound/deal.wav"));

export default {
  name: "Player",
  data() {
    return {
      hand: [],
      handValue: 0,
      cardsInHand: 2,
      acesInHand: 0,
      blackjack: false,
      deck: [],
      cardsDealt: 4,
      money: 30
    };
  },
  methods: {
    hit() {
      audio.play();

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

      if (this.handValue > 21) {
        this.stand();
      }
    },
    stand() {
      EventBus.$emit("stand", this.cardsDealt);
      EventBus.$emit("playerScore", {
        playerScore: this.handValue,
        blackjack: this.blackjack,
      });
    },
    resetData() {
      this.hand = [];
      this.handValue = 0;
      this.cardsInHand = 2;
      this.acesInHand = 0;
      this.blackjack = false;
      this.deck = [];
      this.cardsDealt = 4;
    },
  },
  created() {
    // Get card deck from CardDeck.vue
    EventBus.$on("dealCards", (payload) => {
      audio.play();

      this.deck.push(payload);

      // Deal first 2 cards and count hand value
      this.hand.push(this.deck[0][0], this.deck[0][2]);
      this.handValue = this.hand[0].value + this.hand[1].value;

      // Detect ace in hand
      if (this.hand[0].value == 11 || this.hand[1].value == 11)
        this.acesInHand = 1;
      if (this.hand[0].value == 11 && this.hand[1].value == 11) {
        this.acesInHand = 2;
        this.handValue = 12;
      }

      // Detect blackjack
      if (this.handValue == 21) this.blackjack = true;
    });

    EventBus.$on("playerWins", () => {
      this.money += 1;
    });
    EventBus.$on("dealerWins", () => {
      this.money -= 1;
    });

    EventBus.$on("resetData", () => {
      this.resetData();
    });
  },
  updated() {
    if (this.blackjack) this.stand();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

.player {
  &__cards {
    display: flex;
    flex-direction: column;
    gap: 2rem;
    justify-content: center;
    align-items: center;
    height: 70vh;

    // TRANSITIONS
    .fade-enter-active,
    .fade-leave-active {
      transition: all 0.3s ease-out;
    }

    .fade-enter {
      opacity: 0;
      transform: translateY(-50rem);
    }

    .fade-leave-to {
      opacity: 0;
      position: absolute;
    }
  }

  &__hand-value {
    font-size: 3rem;
    font-weight: 900;
  }

  &__controls {
    display: flex;
    justify-content: center;
    align-content: center;
    gap: 20px;
    height: 8vh;
    button {
      width: 120px;
      margin: 0 10px;
      padding: 15px;
      border: none;
      border-radius: 20px;
      background: rgba(255, 255, 255, .6);
      box-shadow: 0 0.25rem 0 0 rgba(0, 0, 0, .5);
      font-size: 20px;
      cursor: pointer;

      &:hover {
        transform: translateY(-2px);
        box-shadow: 0 0.35rem 0 0 rgba(0, 0, 0, .5);
        background: rgba(255, 255, 255, 1);
      }

      &:active {
        transform: translateY(2px);
        box-shadow: none;
      }
    }
  }

  .bank {
    display: inline-flex;
    justify-content: space-evenly;
    align-items: center;
    width: 120px;
    background: rgba(0, 0, 0, 0.1);
    border-radius: 20px;
    font-size: 24px;
  }
}

.card {
  display: inline-flex;
  height: 100px;
  margin: 5px;
  padding: 3px;
  background-color: #fff;
  // border: 2px solid black;
  border-radius: 5%;
  img {
    max-width: 100%;
    max-height: 100%;
  }
}
</style>
