<script setup>
const emit = defineEmits(["updateTypedLetters", "updateWords"]);
const props = defineProps({
  totalWords: Number,
});

import { ref, onMounted, onBeforeUnmount } from "vue";
import WordItem from "./WordItem.vue";

const allWords = ref([]);
const words = ref([]);
const typedLetters = ref([]);
const currentWordIndex = ref(0);
const isRoundFinished = ref(false);

const fetchWords = async () => {
  const response = await fetch("/src/assets/words.txt");
  const text = await response.text();
  const numOfLines = text.split("\n").length;

  allWords.value = text.split("\n").map((word) => word.trim());

  // Choose random words from the list
  for (let i = 0; i < props.totalWords; i++) {
    const randomIndex = Math.floor(Math.random() * numOfLines);
    words.value.push(allWords.value[randomIndex]);
  }

  typedLetters.value = words.value.map(() => []);
  emit("updateWords", words.value);
};

const handleKeyPress = (key) => {
  // Prevent typing after the last letter of the last word
  if (
    currentWordIndex.value === words.value.length - 1 &&
    typedLetters.value[currentWordIndex.value].length ===
      words.value[currentWordIndex.value].length
  ) {
    return;
  }

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

  // Check if the user has finished typing the last word
  if (
    currentWordIndex.value === words.value.length - 1 &&
    typedLetters.value[currentWordIndex.value].length ===
      words.value[currentWordIndex.value].length
  ) {
    const allCorrect = words.value.every((word, index) => {
      return word.split("").every((letter, letterIndex) => {
        return letter === typedLetters.value[index][letterIndex];
      });
    });
    const moreThanHalfWrong =
      words.value.filter((word, index) => {
        return (
          typedLetters.value[index] &&
          word.split("").some((letter, letterIndex) => {
            return letter !== typedLetters.value[index][letterIndex];
          })
        );
      }).length >=
      words.value.length / 2;

    if (allCorrect) {
      document
        .getElementById("correctWords")
        .classList.remove("text-highlight");
      document.getElementById("correctWords").classList.add("text-correct");
    } else if (moreThanHalfWrong) {
      document
        .getElementById("correctWords")
        .classList.remove("text-highlight");
      document.getElementById("correctWords").classList.add("text-wrong");
    }

    isRoundFinished.value = true;
    emit("updateTypedLetters", typedLetters.value);
  }
};

const handleBackspace = (e) => {
  // Prevent backspace if the current word is fully typed
  if (
    currentWordIndex.value === words.value.length - 1 &&
    typedLetters.value[currentWordIndex.value].length ===
      words.value[currentWordIndex.value].length
  ) {
    return;
  }

  // Handle Ctrl + Backspace to delete all letters of the current word
  if (e.ctrlKey && e.key === "Backspace") {
    typedLetters.value[currentWordIndex.value] = [];
    emit("updateTypedLetters", typedLetters.value);
    return;
  }

  // Handle backspace to delete the last letter of the last word
  if (e.key === "Backspace") {
    typedLetters.value[currentWordIndex.value].pop();
    emit("updateTypedLetters", typedLetters.value);
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
      :isRoundFinished="isRoundFinished"
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
