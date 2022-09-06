<template>
  <div class="parameters-wrapper">
    <SearchInputVue @updateInput="updateQ"/>

    <div class="selector">
      <label>sort: </label>
      <select v-model="parameters.sort">
        <option value="stars">stars</option>
        <option value="forks">forks</option>
        <option value="help-wanted-issues">help-wanted-issues</option>
        <option value="updated">updated</option>
      </select>
    </div>

    <div class="selector">
      <label>order: </label>
      <select v-model="parameters.order">
        <option value="desc">desc</option>
        <option value="asc">asc</option>
      </select>
    </div>

    <div class="selector">
      <label>per_page: </label>
      <select v-model="parameters.per_page">
        <option value="10">10</option>
        <option value="20">20</option>
      </select>
    </div>
  </div>

  <img class="loading" v-show="loading" src="./assets/loading.gif"/>

  <div class="failed" v-if="searchReposFailed">
    search repositories failed
  </div>

  <template v-else>
    <template v-if="repositories.length">
      <List
        :repositories="repositories"
      />

      <div class="pagination-wrapper">
        <Pagination
          class="pagination"
          :current-pagination-number="parameters.page!"
          :max-pagination-number="maxPaginationNumber"
          @update-page="updatePage"
        />
      </div>
    </template>

    <div v-else class="empty">
      no repositories
    </div>
  </template>
</template>

<script setup lang="ts">
import { Octokit } from '@octokit/core'

import {
  reactive, watch, ref
} from 'vue'

import SearchInputVue from './components/SearchInput.vue'
import List from './components/List.vue'
import Pagination from './components/Pagination.vue'

import type { Repository, SearchReposParameters } from './type/repository'

const auth = import.meta.env.VITE_GITHUB_AUTH
const octokit = new Octokit({ auth })

const parameters = reactive<SearchReposParameters>({
  q: '',
  sort: 'stars',
  order: 'desc',
  per_page: 10,
  page: 1
})

const updateQ = (val: string) => {
  parameters.q = val
}

const updatePage = (val: number) => {
  parameters.page = val
}

const loading = ref(false)
const searchReposFailed = ref(false)

const repositories = ref<Repository[]>([])
const maxPaginationNumber = ref(0)

watch(parameters, async () => {
  if (parameters.q === '') {
    repositories.value = []
  } else {
    loading.value = true
    try {
      const { data: { items, total_count: totalCount } } = await octokit.request('GET /search/repositories', parameters)
      repositories.value = items
      // Only the first 1000 search results are available
      const maxCount = totalCount > 1000 ? 1000 : totalCount
      if (parameters.per_page) maxPaginationNumber.value = Math.ceil(maxCount / parameters.per_page)
    } catch (error) {
      searchReposFailed.value = true
      console.error(error)
    }
    loading.value = false
  }
})
</script>

<style lang="scss" scoped>
  .parameters-wrapper {
    display: flex;
    justify-content: space-around;
  }

  .loading, .empty {
    text-align: center;
    padding-top: 20px;
  }

  .loading {
    position: absolute;
    top: 50%;
    left: 50%;
    height: 120px;
    width: 120px;
    transform: translate(-50%, -50%);
  }

  .pagination-wrapper {
    display: flex;
    justify-content: center;
  }
</style>
