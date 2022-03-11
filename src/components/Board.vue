<template>
  <div class="board">
    <transition name="fade">
      <div class="alert" v-if="!isAlertHidden">
        <div>{{ msg }}</div>
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
        this.msg = "PUSH";
      } else if (
        this.playerScore > 21 ||
        (this.dealerScore <= 21 &&
          (this.dealerBlackjack || this.dealerScore > this.playerScore))
      ) {
        this.msg = "LOSE";
        EventBus.$emit("dealerWins");
      } else {
        this.msg = "WIN";
        EventBus.$emit("playerWins");
      }

      this.isAlertHidden = !this.isAlertHidden;
      setTimeout(() => {
        console.log("timeout");
        this.resetData();
      }, 1000);
      
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
  z-index: 1;

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 1rem;
  opacity: 1;

  background: black;

  font-size: 3rem;
  color: white;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.1s ease-out;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>