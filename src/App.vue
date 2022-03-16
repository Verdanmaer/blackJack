<template>
  <div class="app">
    <AnimatedBackground />
    <Board />
    <CardDeck />
    <Dealer />
    <Player />
  </div>
</template>

<script>
import AnimatedBackground from './components/AnimatedBackground.vue';
import Board from "./components/Board";
import CardDeck from "./components/CardDeck.vue";
import Player from "./components/Player";
import Dealer from "./components/Dealer";
import EventBus from './event-bus';

export default {
  name: "App",
  components: {
    AnimatedBackground,
    Board,
    CardDeck,
    Player,
    Dealer,
  },
  mounted() {
    EventBus.$emit("newHand");
  }
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Aldrich&family=Bungee&display=swap");

body {
  font-size: 62.5%;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  background: green;
  z-index: -2;
}

.app {
  width: 70%;
  margin: 0 auto;
  font-family: "Aldrich", sans-serif;
}

.card {
  display: inline-flex;
  height: 100px;
  margin: 5px;
  padding: 3px;
  background-color: #fff;
  border-radius: 5%;
  img {
    max-width: 100%;
    max-height: 100%;
  }
}
</style>