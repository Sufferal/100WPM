<script setup>
import { ref } from "vue";
import WordList from "./components/WordList.vue";
import WordStats from "./components/WordStats.vue";

const totalWords = ref(10);
const typedLetters = ref([]);
const words = ref([]);

const updateTypedLetters = (newTypedLetters) => {
  typedLetters.value = newTypedLetters;
};

const updateWords = (newWords) => {
  words.value = newWords;
};
</script>

<template>
  <div class="app-wrapper">
    <WordStats
      :totalWords="totalWords"
      :typed-letters="typedLetters"
      :words="words"
    />
    <WordList
      :totalWords="totalWords"
      @updateTypedLetters="updateTypedLetters"
      @updateWords="updateWords"
    />
  </div>
</template>

<style scoped>
.app-wrapper {
  width: 80vw;
  margin: 0 auto;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
