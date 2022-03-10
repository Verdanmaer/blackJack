<template>
  <div class="clubs-container">
    <div class="clubs">
      <svg 
        v-for="(suitStyle, i) in suitStyles"
        :key="i"
        class="svg" 
        xmlns="http://www.w3.org/2000/svg" 
        viewBox="0 0 60 60"
        :style="suitStyle">

        <circle cx="18" cy="35" r="14"/>
        <circle cx="30" cy="15" r="14"/>
        <circle cx="42" cy="35" r="14"/>
        <path d="M30,30 Q 30,50 20,60 H40 Q30,50 30,30"/>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      suitStyles: []
    }
  },

  created() {
    const numberOfObjects = 10;
    const minSize = 5;
    const maxSize = 60;
    const minDuration = 15;
    const maxDuration = 35;

    for (let i = 0; i < numberOfObjects; i++) {
      let size = minSize + Math.random() * (maxSize - minSize);
      let top = Math.random() * window.innerHeight;
      top += 'px';
      size += 'px';
      let animationDuration = minDuration + (Math.random() * (maxDuration - minDuration));
      animationDuration += 's';
      let animationDelay = Math.random() * maxDuration;

      this.suitStyles.push({
        position: 'absolute',
        width: size,
        height: size,
        top,
        animationDuration,
        animationDelay
      })
    }
  }
}
</script>

<style scoped>
  .clubs-container {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    overflow: hidden;
    z-index: -1;
  }

  .clubs {
    position: relative;
    width: 100%;
    height: 100%;
  }

  .svg {
    left: -10rem;
    opacity: .2;
    animation: floatRight 3s linear infinite;
  }

  @keyframes floatRight {
    0% {
      transform: translateX(0);
    }
    100% {
      transform: translateX(calc(100vw + 10rem));
    }
  }

</style>