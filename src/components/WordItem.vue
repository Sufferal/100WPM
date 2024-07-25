<script setup>
const props = defineProps({
  name: String,
  typedLetters: Array,
});

function getClass(index) {
  if (index < props.typedLetters.length) {
    return props.name[index] === props.typedLetters[index]
      ? "text-correct"
      : "text-error";
  }
  return "";
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
}
</style>
