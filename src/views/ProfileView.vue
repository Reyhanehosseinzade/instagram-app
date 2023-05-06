<script setup>
import Profile from '../components/Profile.vue'

</script>

<template>
  <main class="xl:w-11/12 mx-auto">
  <Profile :key="$route.params.username" />
  </main>
</template>
