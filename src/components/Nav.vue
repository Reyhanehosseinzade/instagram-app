<script setup>
import { ref } from "vue";
import { RouterLink , useRouter } from "vue-router";
import AuthModal from "./AuthModal.vue";
import {useUserStore} from '../stores/users'
import {storeToRefs} from "pinia"
const userStore = useUserStore()
const {user , loadingUser} = storeToRefs(userStore)
const searchUsername = ref("");
const router = useRouter()
const onSearch = () => {
  if(searchUsername.value){
    router.push(`/Profile/${searchUsername.value}`)
    searchUsername.value = ""
  }
};
const handleLogout = async () => {
 await userStore.handleLogout()
}
const goToUsersProfile = () => {
  router.push(`/profile/${user.value.username}`)
}

</script>

<template>
  <div class="bg-slate-800 flex justify-between items-center flex-wrap sm:px-11 py-2 sm:py-3 px-3">
    <RouterLink to="/" class="text-blue-400 text-[1.5rem] w-1/3 md:w-1/4 text-center md:text-start"
      >instagram</RouterLink
    >
 <div class="w-3/4 md:w-2/4 mx-auto order-1 md:order-none mt-3 md:mt-0">
  <a-input-search
      v-model:value="searchUsername"
      placeholder="username..."
      @search="onSearch"
    />
 </div>
 
  
  

    <section class="w-2/3  md:w-1/4 flex justify-end items-center mt-1 md:mt-0">
      <div v-if="!loadingUser">
        <div class="flex justify-center space-x-3 items-center" v-if="!user">
        <AuthModal :isLogin="false" />
        <AuthModal :isLogin="true"/>
     </div>
     <div class=" flex justify-center space-x-3 items-center" v-else>
      <AButton  type="primary" @click="goToUsersProfile">profile</AButton>
      <AButton  @click="handleLogout" >logout</AButton>
     </div>
      </div>
    </section>
  </div>
</template>

