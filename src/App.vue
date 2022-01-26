<template>
  <div id="app">
    <Board />
    <CardDeck />
    <Player />
    <Dealer />
  </div>
</template>

<script>
import Board from "./components/Board";
import CardDeck from "./components/CardDeck.vue";
import Player from "./components/Player";
import Dealer from "./components/Dealer";

export default {
  name: "App",
  components: {
    Board,
    CardDeck,
    Player,
    Dealer,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Aldrich&family=Bungee&display=swap");

body {
  background: linear-gradient(
    to right,
    rgba(0, 41, 5, 0.788),
    rgba(1, 110, 6, 0.5) 20% 80%,
    rgba(0, 41, 5, 0.788)
  ),
  url("assets/background/bg.png");
}

#app {
  margin-left: 250px;
  margin-right: 250px;
  font-family: "Aldrich", sans-serif;
  text-align: center;
}
</style>
