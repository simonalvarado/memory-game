<template>
  <div
    class="memory-card"
  >
    <div v-if="flipped" class="memory-card__front">
      <img :src="image" class="memory-card__image" alt="Card Front" />
    </div>
    <div v-else class="memory-card__back">
    </div>
  </div>
</template>
<script>
export default {
  name: 'MemoryCard',
  props: {
    image: {
      type: String,
      required: true,
    },
    flipped: {
      type: Boolean,
      default: false,
    },
    found: {
      type: Boolean,
      default: false,
    },
  },
};
</script>
<style scoped>
.memory-card {
    width: 100%;
    height: 0;
    padding-top: 100%;
    position: relative;
    cursor: pointer;
}
.memory-card__front,
.memory-card__back {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    /* backface-visibility: hidden;
    transition: transform 0.5s ease; */
}
.memory-card__front {
    /* transform: rotateY(0deg); */
    /* z-index: 21; */
}
.memory-card__back {
    /* transform: rotateY(180deg); */
    /* z-index: 1; */
    background-color: rgb(10, 34, 55);
    border-radius: 5px;
}
/* .memory-card--flipped .memory-card__front {
    transform: rotateY(-180deg);
    z-index: 1;
}
.memory-card--flipped .memory-card__back {
    transform: rotateY(0deg);
    z-index: 2;
}
.memory-card--found {
    pointer-events: none;
    opacity: 0.5;
} */
.memory-card__image {
    width: 100%;
    height: 100%;
    object-fit: cover;
}
</style>
