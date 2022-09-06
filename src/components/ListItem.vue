<template>
  <div class="repository">
    <a class="name" target="_blank" :href="repository.html_url">
      {{ repository.name }}
    </a>
    <div class="description">
      {{ repository.description }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { PropType } from 'vue'
import type { Repository } from '../type/repository'

defineProps({
  repository: {
    type: Object as PropType<Repository>,
    required: true
  }
})
</script>

<style lang="scss" scoped>
  .repository {
    margin: 10px 0px;
    border: 1px solid #efefef;
    padding: 10px;
  }
  .description {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
  }
</style>
