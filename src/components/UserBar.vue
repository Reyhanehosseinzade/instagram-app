<script setup>
import { defineProps } from "vue";
import UploadPhotoModal from "./UploadPhotoModal.vue";
import { useRoute } from "vue-router";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import { supabase } from "../supabase";
const route = useRoute();
const userStore = useUserStore();
const { user } = storeToRefs(userStore);
const { username: profileUsername } = route.params;
const props = defineProps(["user", "userInfo", "addNewPost", "isFollowing" , "updateFollowing"]);

const followUser = async () => {
    props.updateFollowing(true)
    await supabase.from("followers_following").insert({
    follower_id: user.value.id,
    following_id: props.user.id,
  });
};
const unfollowUser = async () => {
  props.updateFollowing(false)
 await supabase.from("followers_following")
  .delete()
  .eq("follower_id" , user.value.id)
  .eq("following_id" , props.user.id);
};

</script>

<template>
  <div class="mt-3 p-2 border-b-2 border-b-blue-500" v-if="props.user">
    <a-typography-title :level="3">{{
      props.user.username
    }}</a-typography-title>
    <div class="grid grid-cols-10">
      <div class="col-span-6 flex justify-start items-center">
        <a-typography-title class="border-r border-slate-400 px-3" :level="5"
          >{{ props.userInfo.posts }} posts
        </a-typography-title>
        <a-typography-title class="border-r border-slate-400 px-3" :level="5"
          >{{ props.userInfo.following }} following</a-typography-title
        >
        <a-typography-title class="px-4" :level="5"
          >{{ props.userInfo.followers }} followers</a-typography-title
        >
      </div>
      <div class="col-span-4 flex justify-end">
        <div v-if="user">
          <UploadPhotoModal
            v-if="profileUsername === user.username"
            :addNewPost="addNewPost"
          />
          <div v-else>
            <a-button v-if="!props.isFollowing" @click="followUser"> Follow </a-button>
            <a-button v-else @click="unfollowUser"> Following </a-button>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="mt-3 p-2 border-b-2 border-b-blue-500 text-center" v-else>
    <a-typography-title :level="3">USER NOT FOUND</a-typography-title>
  </div>
</template>
<style>
h5 {
  margin: 0 !important;
}
</style>
