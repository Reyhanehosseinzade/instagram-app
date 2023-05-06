<script setup>
import { ref, defineProps, reactive } from "vue";
import { storeToRefs } from "pinia";

import { useUserStore } from "../stores/users.js";

const userStore = useUserStore();

const { errorMessage, loading, user } = storeToRefs(userStore);

const props = defineProps(["isLogin"]);
const visible = ref(false);

const userCrredential = reactive({
  email: "",
  username: "",
  password: "",
});

const showModal = () => {
  visible.value = true;
};
const clearUserCredentialsInput = () =>{
  userCrredential.email = ""
    userCrredential.username = ""
    userCrredential.password = ""
    userStore.clearErrorMessage();
}
const handleOk = async () => {
  if(props.isLogin){
    await userStore.handleLogin({
      password : userCrredential.password,
      email :userCrredential.email
    });
  }else{
    await userStore.handleSignup(userCrredential);
  }
  if (user.value) {
    clearUserCredentialsInput()
    visible.value = false;
  }
};

const handleCancel = () => {
  clearUserCredentialsInput()
  visible.value = false;
};

const title = props.isLogin ? "login" : "signup";
</script>

<template>
  <div>
    <a-button type="primary" @click="showModal">{{
      title
    }}</a-button>
    <a-modal v-model:visible="visible" :title="title" @ok="handleOk">
      <template #footer>
        <a-button key="back" @click="handleCancel">cancel</a-button>
        <a-button
          :disabled="loading"
          key="submit"
          type="primary"
          :loading="loading"
          @click="handleOk"
          >Submit</a-button
        >
      </template>
      <div>
        <div class="grid gap-y-3" v-if="!loading">
          <a-input
            v-if="!isLogin"
            v-model:value="userCrredential.username"
            placeholder="Username"
          />
          <a-input v-model:value="userCrredential.email" placeholder="Email" />
          <a-input-password
            v-model:value="userCrredential.password"
            placeholder="Password"
          />
        </div>
        <div v-else class="flex items-center justify-center h-[150px]">
          <a-spin />
        </div>

        <a-typography-Text v-if="errorMessage" type="danger">{{
          errorMessage
        }}</a-typography-Text>
      </div>
    </a-modal>
  </div>

</template>
