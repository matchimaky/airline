<script setup lang="ts">
   defineProps({
        resource: {
            type: String,
            required: true,
            default: 'page'
        }
    })

</script>
<template>
    <h1>Oops!</h1>
    <h3>The {{ resource }} you're looking for is not here.</h3>
    <router-link :to="{name: 'passenger-list-view'}">Back to the homepage</router-link>
</template>