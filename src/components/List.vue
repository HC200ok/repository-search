<template>
  <div class="repositories">
    <ListItem v-for="(item, index) in repositories" :repository="item" :key="index"/>
  </div>
</template>

<script lang="ts" setup>
import { PropType } from 'vue'
import type { Repository } from '../type/repository'
import ListItem from './ListItem.vue'

defineProps({
  repositories: {
    type: Array as PropType<Repository[]>,
    required: true
  }
})
</script>
