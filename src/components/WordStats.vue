<script setup>
import { computed } from "vue";

const props = defineProps({
  totalWords: Number,
  typedLetters: Array,
  words: Array,
});

const correctWords = computed(() => {
  return props.words.filter((word, index) => {
    return (
      props.typedLetters[index] &&
      word.split("").every((letter, letterIndex) => {
        return letter === props.typedLetters[index][letterIndex];
      })
    );
  }).length;
});
</script>

<template>
  <h2 id="perfectWords">
    <span id="correctWords" class="text-highlight">{{ correctWords }}</span>
    /
    <span id="totalWords">{{ totalWords }}</span>
  </h2>
</template>

<style scoped>
#perfectWords {
  font-size: 3rem;
  margin-bottom: 2rem;
}
</style>
