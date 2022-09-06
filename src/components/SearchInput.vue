<template>
  <input v-model="query" placeholder="search" type="text">
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

const query = ref('')

const emits = defineEmits(['updateInput'])

let timeout: ReturnType<typeof setTimeout> | undefined

const throttleFunction = (func: () => void, delay: number) => {
  if (timeout) return
  timeout = setTimeout(() => {
    func()
    timeout = undefined
  }, delay)
}

watch(query, () => {
  throttleFunction(() => {
    emits('updateInput', query.value)
  }, 1000)
})
</script>
