<script setup lang="ts">
import { RouterLink, useRouter, useRoute } from 'vue-router'
import { useMessageStore } from './stores/message'
import { storeToRefs } from 'pinia'

const store = useMessageStore()
const { message } = storeToRefs(store)
import { ref } from 'vue'
const router = useRouter()
const route = useRoute()
const pageSize = ref(getPageSize())

// Get the page size from the query parameter or use a default value
function getPageSize() {
  const pageSize = Number(route.query.pageSize)
  return isNaN(pageSize) ? 2 : pageSize
}

// Update the URL with the new page size
const updatePageSize = (newPageSize: number) => {
  router.push({ name: 'passenger-list-view', query: { ...route.query, pageSize: newPageSize } })
}
</script>

<template>
  <div id="layout">
    <header>
      <div id="flashMessage" v-if="message">
        <h4>{{ message }}</h4>
      </div>
      <div class="wrapper">
        <HelloWorld msg="You did it!" />

        <nav>
          <RouterLink :to="{ name: 'passenger-list-view' }">Home</RouterLink>
        </nav>
      </div>
      <!-- Page Size Selection -->
      <div class="page-size-links">
        <label>Set Page Size: </label>
        <RouterLink
          v-for="size in [1, 2, 3, 4, 5, 6]"
          :key="size"
          :to="{ name: 'passenger-list-view', query: { ...route.query, pageSize: size } }"
        >
          {{ size }}
        </RouterLink>
      </div>
    </header>
    <RouterView />
  </div>
</template>

<style>
#layout {
  font-family: Avenir, Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}
nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}

.page-size-links {
  margin-top: 20px;
}

.page-size-links a {
  margin: 0 5px;
  text-decoration: none;
  color: #2c3e50;
}

.page-size-links a.router-link-exact-active {
  color: #42b983;
}
@keyframes yellow-fade {
  from {
    background-color: yellow;
  }
  to {
    background-color: transparent;
  }
}
#flashMessage {
  animation: yellow-fade 3s ease-in-out;
}
</style>
