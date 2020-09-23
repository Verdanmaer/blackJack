<template>
  <div class="player">
    <div class="container">
      <transition-group name="fade">
        <div v-for="card in hand" :key="card.texture" class="card">
          <img :src="require(`../assets/cards/${card.texture}.png`)" />
        </div>
      </transition-group>
    </div>

    <div class="btn">
      <!-- CHANGE CLASS NAME -->
      <div class="buttons-play" v-if="isPlayingPhase">
        <!-- -->
        <button @click="hit">Hit</button>
        <button @click="stand">Stand</button>
        <span class="hand-value">Hand value: {{ handValue }}</span>
      </div>
      <div class="deal">
        <button v-if="isBettingPhase" @click="deal">Deal</button>
      </div>
      <div class="chip" v-if="isBettingPhase">
        <button class="chip__5" @click="currentBet = 5">5</button>
        <button class="chip__10" @click="currentBet = 10">10</button>
        <button class="chip__25" @click="currentBet = 25">25</button>
        <button class="chip__100" @click="currentBet = 100">100</button>
      </div>
    </div>

    <!--<div>{{ money }}</div>-->
    <div class="info" v-if="isBettingPhase">
      <div>Bet: {{ currentBet }}$</div>
      <div>Money: {{ money }}$</div>
    </div>
  </div>
</template>

<script>
import EventBus from "./event-bus";

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
      money: 2500,
      currentBet: 5,
      isPlayingPhase: false,
      isBettingPhase: true,
    };
  },
  methods: {
    deal() {
      this.isBettingPhase = !this.isBettingPhase;
      this.isPlayingPhase = !this.isPlayingPhase;
      EventBus.$emit("newHand");
    },
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
      this.isPlayingPhase = false;
      this.isBettingPhase = true;
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
      this.money += this.currentBet;
    });
    EventBus.$on("dealerWins", () => {
      this.money -= this.currentBet;
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
.container {
  display: flex;
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
  // TRANSITIONS
  .fade-enter-active,
  .fade-leave-active {
    transition: all 0.8s ease-out;
  }

  .fade-enter {
    opacity: 0;
    transform: translateX(100px);
  }

  .fade-leave-to {
    opacity: 0;
    position: absolute;
    transform: translateX(-250px);
  }
}
.btn {
  button {
    height: 150px;
    width: 150px;
    margin: 20px;
    padding: 20px;
    border-width: 6px;
    border-color: black;
    border-radius: 50%;
    outline: none;
    font-size: 36px;
    cursor: pointer;

    &:hover {
      transform: scale(1.05);
      opacity: 0.85;
    }

    &:active {
      transform: scale(0.95);
    }
  }
  .deal {
    position: absolute;
    top: 10%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  .chip {
    position: absolute;
    top: 70%;
    left: 50%;
    transform: translate(-50%, -50%);
    min-width: 100%;
    .chip__5 {
      background-image: url("../assets/chips/chip_5.png");
      background-size: contain;
    }
    .chip__10 {
      background-image: url("../assets/chips/chip_10.png");
      background-size: contain;
    }
    .chip__25 {
      background-image: url("../assets/chips/chip_25.png");
      background-size: contain;
    }
    .chip__100 {
      background-image: url("../assets/chips/chip_100.png");
      background-size: contain;
    }
  }
}
.info {
  position: absolute;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: orange;
  font-size: 64px;
}
</style>
