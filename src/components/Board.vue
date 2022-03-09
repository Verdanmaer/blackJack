<template>
  <div class="board">
    <transition name="fade">
      <div class="alert" v-if="!isAlertHidden" @click="resetData()">
        <div>Player score: {{ playerScore }}</div>
        <div>{{ msg }}</div>
        <div>Dealer score: {{ dealerScore }}</div>
      </div>
    </transition>
  </div>
</template>

<script>
import EventBus from "../event-bus";

export default {
  name: "Board",
  data() {
    return {
      playerScore: 0,
      playerBlackjack: false,
      dealerScore: 0,
      dealerBlackjack: false,
      isAlertHidden: true,
      msg: "",
    };
  },
  methods: {
    selectWinner() {
      if (
        (this.playerBlackjack && this.dealerBlackjack) ||
        (this.playerScore == this.dealerScore &&
          this.playerScore <= 21 &&
          this.playerBlackjack == this.dealerBlackjack)
      ) {
        this.msg = "Push";
        console.log("PUSH");
      } else if (
        this.playerScore > 21 ||
        (this.dealerScore <= 21 &&
          (this.dealerBlackjack || this.dealerScore > this.playerScore))
      ) {
        this.msg = "Dealer wins";
        EventBus.$emit("dealerWins");
      } else {
        this.msg = "Player wins";
        EventBus.$emit("playerWins");
      }

      this.isAlertHidden = !this.isAlertHidden;
    },
    resetData() {
      EventBus.$emit("resetData");
      this.isAlertHidden = !this.isAlertHidden;
      EventBus.$emit("newHand");
    },
  },
  created() {
    // Get player score
    EventBus.$on("playerScore", (payload) => {
      this.playerScore = payload.playerScore;
      this.playerBlackjack = payload.blackjack;

      this.selectWinner();
    });
    // Get dealer score
    EventBus.$on("dealerScore", (payload) => {
      this.dealerScore = payload.dealerScore;
      this.dealerBlackjack = payload.blackjack;
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.alert {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  z-index: 1;

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  opacity: 0.85;

  width: 100%;
  height: 100%;

  background-image: radial-gradient(rgba(0, 0, 0, 1), rgba(0, 0, 0, 0.85));

  font-size: 48px;
  color: gold;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease-out;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>