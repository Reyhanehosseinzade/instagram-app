<script setup>
import Cards from "./Cards.vue";
import LogInMessage from './LogInMessage.vue'
import { useUserStore } from "../stores/users.js";
import { storeToRefs } from "pinia";

const userStore = useUserStore();

const { user, loadingUser } = storeToRefs(userStore);


</script>
<template>
  <div v-if="!loadingUser">
    <Cards v-if="user" />
    <LogInMessage v-else />
  </div>
  <div v-else class="container grid place-items-center h-screen">
    <a-spin /> 
  </div>
</template>
