<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import WordItem from "./WordItem.vue";

const allWords = ref([]);
const words = ref([]);
const typedLetters = ref([]);
const currentWordIndex = ref(0);

const fetchWords = async () => {
  const response = await fetch("/src/assets/words.txt");
  const text = await response.text();
  const numOfLines = text.split("\n").length;
  const numOfWords = 10;

  allWords.value = text.split("\n").map((word) => word.trim());

  // Choose random words from the list
  for (let i = 0; i < numOfWords; i++) {
    const randomIndex = Math.floor(Math.random() * numOfLines);
    words.value.push(allWords.value[randomIndex]);
  }

  typedLetters.value = words.value.map(() => []);
};

const handleKeyPress = (key) => {
  if (key === " ") {
    // Only move to the next word if the current word is fully typed
    if (
      typedLetters.value[currentWordIndex.value].length ===
        words.value[currentWordIndex.value].length &&
      currentWordIndex.value < words.value.length - 1
    ) {
      currentWordIndex.value++;
    }
  } else if (
    typedLetters.value[currentWordIndex.value].length <
    words.value[currentWordIndex.value].length
  ) {
    typedLetters.value[currentWordIndex.value].push(key);
  }
};

const handleBackspace = (e) => {
  // Handle Ctrl + Backspace to delete all letters of the current word
  if (e.ctrlKey && e.key === "Backspace") {
    typedLetters.value[currentWordIndex.value] = [];
    return;
  }

  // Handle backspace to delete the last letter of the last word
  if (e.key === "Backspace") {
    typedLetters.value[currentWordIndex.value].pop();
    return;
  }
};

const isKeyValid = (e) => {
  // Ignore if the key is not a letter or Space or Dash
  if (!e.key.match(/^[a-zA-Z]$/) && e.key !== " " && e.key !== "-") {
    return false;
  }

  return true;
};

const onKeyDown = (e) => {
  handleBackspace(e);

  if (!isKeyValid(e)) {
    return;
  }

  handleKeyPress(e.key);
};

onMounted(() => {
  window.addEventListener("keydown", onKeyDown);
  fetchWords();
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", onKeyDown);
});
</script>

<template>
  <div class="word-list">
    <WordItem
      v-for="(word, index) in words"
      :key="index"
      :name="word"
      :typedLetters="typedLetters[index]"
      :currentWordIndex="currentWordIndex"
      :wordIndex="index"
    />
  </div>
</template>

<style scoped>
.word-list {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 3rem;
}
</style>
