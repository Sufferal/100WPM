<script setup>
import { onMounted, onBeforeUnmount, ref } from "vue";
import Word from "./components/Word.vue";

const word = "Perfection";
const typedLetters = ref([]);

const onKeyDown = (e) => {
  // Handle Ctrl + Backspace
  if (e.ctrlKey && e.key === "Backspace") {
    typedLetters.value = [];
    return;
  }

  // Handle backspace
  if (e.key === "Backspace") {
    typedLetters.value.pop();
    return;
  }

  // Ignore if the key is not a letter
  if (!e.key.match(/^[a-zA-Z]$/)) {
    return;
  }

  if (typedLetters.value.length < word.length) {
    typedLetters.value.push(e.key);
  }

  console.log(e.key);
};

onMounted(() => {
  window.addEventListener("keydown", onKeyDown);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", onKeyDown);
});
</script>

<template>
  <div class="app-wrapper">
    <Word :name="word" :typedLetters="typedLetters" />
  </div>
</template>

<style scoped>
.app-wrapper {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
