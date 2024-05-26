<script setup>
import { computed } from 'vue'

const props = defineProps({
  diceNumber: { type: Number, required: true },
  hidden: { type: Boolean, required: true }
})

const dicePic = computed(() => {
  return `./src/assets/dice-${props.diceNumber}.png`
})
</script>

<template>
  <img :src="dicePic" alt="Playing dice" class="dice" :class="{ hidden: hidden }" />
</template>
