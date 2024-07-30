<script setup>
import { computed } from "vue";

const props = defineProps({
  name: String,
  typedLetters: Array,
  currentWordIndex: Number,
  wordIndex: Number,
  isRoundFinished: Boolean,
});

const currentLetterIndex = computed(() => props.typedLetters.length - 1);

function getClass(index) {
  let className = "";

  if (
    index === 0 &&
    props.typedLetters.length === 0 &&
    props.currentWordIndex === props.wordIndex
  ) {
    className += "text-caret-first";
  }

  if (props.isRoundFinished) {
    className += "text-finish ";
  }
  if (index < props.typedLetters.length) {
    const isCurrentLetter = index === currentLetterIndex.value;
    const isCurrentWord = props.currentWordIndex === props.wordIndex;

    if (props.name[index] === props.typedLetters[index]) {
      className += `text-correct ${isCurrentWord && isCurrentLetter ? "text-caret" : ""}`;
    } else {
      className += `text-wrong ${isCurrentWord && isCurrentLetter ? "text-caret" : ""}`;
    }
  }

  return className;
}
</script>

<template>
  <h2 class="currentWord">
    <span
      v-for="(letter, index) in props.name"
      :key="index"
      :class="getClass(index)"
    >
      {{ letter }}
    </span>
  </h2>
</template>

<style scoped>
.currentWord {
  font-size: 3rem;
  letter-spacing: 0.1rem;
}
</style>
