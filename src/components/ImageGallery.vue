<script setup>
import { defineProps } from "vue";
const {VITE_BASE_PHOTO_URL} = import.meta.env
const props = defineProps(["posts"]);
</script>

<template>
  <div class="pb-20 pt-3 flex flex-wrap justify-start">
    <img
      class="rounded-lg w-1/2 h-52 p-1 sm:w-1/2 md:w-1/3 sm:h-80 lg:h-96 object-cover border-red-500 border-4 m-0 sm:p-2"
      v-for="post in props.posts"
      :key="post.id"
      :src="`${VITE_BASE_PHOTO_URL}${post.url}`"
      :alt="post.id"
    />
  </div>
</template>


<style scoped>
img{
  border-radius: 10px !important;
}

</style>
