<script setup>
import { computed } from "vue";

const props = defineProps({
  name: String,
  typedLetters: Array,
  currentWordIndex: Number,
  wordIndex: Number,
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

  if (index < props.typedLetters.length) {
    const isCurrentLetter = index === currentLetterIndex.value;
    const isCurrentWord = props.currentWordIndex === props.wordIndex;

    return props.name[index] === props.typedLetters[index]
      ? `text-correct ${isCurrentWord && isCurrentLetter ? "text-caret" : ""}`
      : `text-error ${isCurrentWord && isCurrentLetter ? "text-caret" : ""}`;
  }

  return className;
}
</script>

<template>
  <h2 id="currentWord">
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
#currentWord {
  font-size: 3rem;
  letter-spacing: 0.1rem;
}
</style>
