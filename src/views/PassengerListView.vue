<script setup lang="ts">
import PassengersCard from '@/components/PassengerCard.vue'
import type { Passenger } from '@/types'
import { ref, watchEffect, computed, onMounted } from 'vue'
import PassengerService from '@/services/PassengerService'
import type { AxiosResponse } from 'axios'
import { RouterLink, useRouter, useRoute } from 'vue-router'

const passengers = ref<Passenger[] | null>(null)
const totalPassenger = ref(0)
const hasNextPage = computed(() => {
  const totalPages = Math.ceil(totalPassenger.value / getPageSize())
  return page.value < totalPages
})
const route = useRoute()

const router = useRouter()
const page = computed(() => props.page)

const props = defineProps<{
  page: number
  limit: number
}>()
const getPageSize = () => {
  const pageSize = Number(route.query.pageSize)
  return isNaN(pageSize) ? 2 : pageSize
}

// watchEffect(() => {
//   PassengerService.getPassengers(props.limit, props.page)
//     .then((response: AxiosResponse<Passenger[]>) => {
//       passengers.value = response.data
//       totalPassenger.value = parseInt(response.headers['x-total-count'], 10)
//     })
//     .catch((error) => {
//       console.log(error)
//       if (error.response && error.response.status === 404) {
//         router.push({ name: '404-resource', params: { resource: 'page' } })
//       } else {
//         router.push({ name: 'network-error' })
//       }
//     })
// })
onMounted(() => {
  watchEffect(() => {
    PassengerService.getPassengers(getPageSize(), page.value)
      .then((response) => {
        passengers.value = response.data
        totalPassenger.value = Number(response.headers['x-total-count'])
      })
      .catch((error) => {
        console.log('There was an error!', error)
      })
  })
})
// Update the URL with the new page size
const updatePageSize = (pageSize: number) => {
  router.push({ query: { ...route.query, pageSize } })
}
</script>

<template>
  <h1>Passengers</h1>
  <div class="passengers">
    <PassengersCard v-for="passenger in passengers" :key="passenger.id" :passenger="passenger" />
    <div class="pagination">
      <RouterLink
        :to="{ name: 'passenger-list-view', query: { page: props.page - 1 } }"
        rel="prev"
        v-if="props.page != 1"
        >Prev page</RouterLink
      >
      <RouterLink
        :to="{ name: 'passenger-list-view', query: { page: props.page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        >Next page</RouterLink
      >
    </div>
  </div>
</template>

<style scoped>
.passengers {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.pagination {
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}
</style>
