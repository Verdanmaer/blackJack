<template>
  <div class="app">
    <Board />
    <CardDeck />
    <Dealer />
    <Player />
  </div>
</template>

<script>
import Board from "./components/Board";
import CardDeck from "./components/CardDeck.vue";
import Player from "./components/Player";
import Dealer from "./components/Dealer";
import EventBus from './event-bus';

export default {
  name: "App",
  components: {
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

<style>
@import url("https://fonts.googleapis.com/css2?family=Aldrich&family=Bungee&display=swap");

body {
  font-size: 62.5%;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  background: linear-gradient(
    to right,
    rgba(0, 41, 5, 0.788),
    rgba(1, 110, 6, 0.5) 20% 80%,
    rgba(0, 41, 5, 0.788)
  ),
  url("assets/background/bg.png");
}

.app {
  width: 70%;
  margin: 0 auto;
  font-family: "Aldrich", sans-serif;
}
</style>
