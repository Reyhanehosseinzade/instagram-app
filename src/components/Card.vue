<script setup>
import { defineProps } from 'vue';
const props = defineProps(['post'])
const {VITE_BASE_PHOTO_URL} = import.meta.env
</script>

<template>
    <a-card hoverable class="sm:w-10/12 md:w-6/12 lg:w-5/12 mx-auto">
      <template #cover>
        <img
          alt=""
          :src="`${VITE_BASE_PHOTO_URL}${post.url}`"
        />
      </template>
      <a-card-meta>
        <template #description>{{ post.caption }}</template>
      </a-card-meta>
    </a-card>
</template>
