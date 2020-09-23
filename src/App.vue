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
  min-width: 1000px;
  background-image: url("assets/background/bg.png");
  background-color: rgb(22, 58, 13);
}

#app {
  margin-left: 250px;
  margin-right: 250px;
  font-family: "Aldrich", sans-serif;
  text-align: center;
}
</style>
