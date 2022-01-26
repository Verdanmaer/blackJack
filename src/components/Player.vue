<template>
  <div class="player">
    <div class="container">
      <transition-group name="fade">
        <div v-for="card in hand" :key="card.texture" class="card">
          <img :src="require(`../assets/cards/${card.texture}.png`)" />
        </div>
      </transition-group>
    </div>
    <div v-if="isPlayingPhase">
        <button @click="hit">Hit</button>
        <button @click="stand">Stand</button>
        <span class="hand-value">Hand value: {{ handValue }}</span>
    </div>

    <div class="betting" v-if="isBettingPhase">
      <div class="chip">
        <div class="flex-inner">
          <input type="radio" id="r1" name="rr"/>
          <label for="r1" class="chip__5" @click="currentBet = 5"></label>
          <input type="radio" id="r2" name="rr"/>
          <label for="r2" class="chip__10" @click="currentBet = 10"></label>
        </div>
        <div class="flex-inner">
          <input type="radio" id="r3" name="rr"/>
          <label for="r3" class="chip__25" @click="currentBet = 25"></label>
          <input type="radio" id="r4" name="rr"/>
          <label for="r4" class="chip__100" @click="currentBet = 100"></label>
        </div>
        <input type="submit" value="Deal" class="deal" @click="deal">
      </div>
      <div class="info" >
        <div>Bet: {{ currentBet }}$</div>
        <div>Money: {{ money }}$</div>
      </div>
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

.betting {
  height: 600px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;

  .chip {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;

    input[type="radio"] {
      display: none;
    }

    input[type="radio"] + label {
      min-width: 150px;
      min-height: 150px;
      margin: 30px;
      background-color: white;
      border: 3px solid black;
      border-radius: 50%;
      box-shadow: 8px 8px 2px black;
      cursor: pointer;
    }

    input[type="radio"]:checked + label {
      border: 3px solid black;
      transform: scale(1.1);
    }

    .chip__5 {
      background-image: url("../assets/chips/chip_5.png");
      background-size: cover;
    }
    .chip__10 {
      background-image: url("../assets/chips/chip_10.png");
      background-size: cover;
    }
    .chip__25 {
      background-image: url("../assets/chips/chip_25.png");
      background-size: cover;
    }
    .chip__100 {
      background-image: url("../assets/chips/chip_100.png");
      background-size: cover;
    }

    .deal{
      position: absolute;
      top: 70%;
      left: 50%;
      transform: translate(-50%, -50%);
    }

    .flex-inner {
      display: flex;
    }
  }
  .info {
    color: orange;
    font-size: 64px;
  }
}

@media only screen and (max-width: 992px) {
  .betting{
    .chip{
      input[type="radio"] + label {
        min-width: 100px;
        min-height: 100px;
        margin: 20px;
      }
    }
  }
}
</style>
