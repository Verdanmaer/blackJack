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

html {
  font-size: 100%;
}

body {
  box-sizing: border-box;
  min-width: 200px;
  
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
  height: 10rem;
  margin: .5rem;
  padding: .3rem;
  background-color: #fff;
  border-radius: 5%;
  img {
    max-width: 100%;
    max-height: 100%;
  }
}

@media (max-width: 575.98px) {
  html {
    font-size: 75%;
  }

  .app {
    width: 100%;
  }
}
</style>