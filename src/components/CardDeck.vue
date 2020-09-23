<template>
  <div class="cardDeck"></div>
</template>

<script>
import EventBus from "./event-bus";

export default {
  name: "CardDeck",
  data() {
    return {
      cards: [],
    };
  },

  methods: {
    shuffleDeck(cards) {
      for (let i = 0; i < 500; i++) {
        let loc1 = Math.floor(Math.random() * cards.length);
        let loc2 = Math.floor(Math.random() * cards.length);

        let tmp = cards[loc1];
        cards[loc1] = cards[loc2];
        cards[loc2] = tmp;
      }
    },
    dealCards() {
      EventBus.$emit("dealCards", this.cards);
    },
  },
  created() {
    let suits = ["spades", "diamonds", "clubs", "hearts"];
    // prettier-ignore
    let values = [/* ace - one or eleven */11, 2, 3, 4, 5, 6, 7, 8, 9, 10, 10, 10, 10];
    // prettier-ignore
    let textures = [
      ["AS", "2S", "3S", "4S", "5S", "6S", "7S", "8S", "9S", "10S", "JS", "QS", "KS"],
      ["AoD", "2D", "3D", "4D", "5D", "6D", "7D", "8D", "9D", "10D", "JD", "QD", "KD"],
      ["AC", "2C", "3C", "4C", "5C", "6C", "7C", "8C", "9C", "10C", "JC", "QC", "KC"],
      ["AH", "2H", "3H", "4H", "5H", "6H", "7H", "8H", "9H", "10H", "JH", "QH", "KH"]
    ];

    // create a deck
    for (let i = 0; i < suits.length; i++) {
      for (let j = 0; j < values.length; j++) {
        let card = {
          value: values[j],
          suit: suits[i],
          texture: textures[i][j],
        };
        this.cards.push(card);
      }
    }
    this.shuffleDeck(this.cards);

    EventBus.$on("newHand", () => {
      this.shuffleDeck(this.cards);
      this.dealCards();
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
