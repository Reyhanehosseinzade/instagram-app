<script setup>
import UserBar from "./UserBar.vue";
import ImageGallery from "./ImageGallery.vue";
import { supabase } from "../supabase";
import { ref, onMounted, watch, reactive } from "vue";
import { useRoute } from "vue-router";
import { useUserStore } from "../stores/users.js";
import { storeToRefs } from "pinia";

const userStore = useUserStore();
const { user: loggedInUser } = storeToRefs(userStore);
const route = useRoute();
const user = ref(null);
const isFollowing = ref(false);
const loading = ref(false);
const { username } = route.params;
const posts = ref([]);

const userInfo = reactive({
  posts: null,
  followers: null,
  following: null,
});

const addNewPost = (post) => {
  posts.value.unshift(post);
};

const updateFollowing = (follow) => {
  isFollowing.value = follow;
};

const fetchData = async () => {
  loading.value = true;
  const { data: userData } = await supabase
    .from("users")
    .select()
    .eq("username", username)
    .single();

  if (!userData) {
    loading.value = false;
    return (user.value = null);
  }
  user.value = userData;

  const { data: postsData } = await supabase
    .from("posts")
    .select()
    .eq("owner_id", user.value.id);

  posts.value = postsData;


 const followerCount = await fetchFollowersCount()
 const followingCount = await fetchFollowingCount()

 userInfo.followers = followerCount
 userInfo.following = followingCount
 userInfo.posts = posts.value.length


 loading.value = false;
};

const fetchFollowersCount = async () => {
  const {count} = await supabase
  .from("followers_following")
  .select('*' , {count:'exact'})
  .eq("following_id", user.value.id)
  return count
}
const fetchFollowingCount = async () => {
  const {count} = await supabase
  .from("followers_following")
  .select('*' , {count:'exact'})
  .eq("follower_id", user.value.id)
  return count
}


const fetchIsFollowing = async () => {
  if (loggedInUser.value && loggedInUser.value.id !== user.value.id) {
    const { data } = await supabase
      .from("followers_following")
      .select()
      .eq("follower_id", loggedInUser.value.id)
      .eq("following_id", user.value.id)
      .single();

    if (data) isFollowing.value = true;
  }
};
onMounted(() => {
  fetchData();
});



watch(loggedInUser, () => {
  fetchIsFollowing();
});

</script>
<template>
  <div v-if="!loading">
    <UserBar
      :key="$route.params.username"
      :user="user"
      :userInfo="userInfo"
      :addNewPost="addNewPost"
      :isFollowing="isFollowing"
      :updateFollowing="updateFollowing"
    />
    <ImageGallery :posts="posts" />
  </div>
  <div v-else class="flex p-10 justify-center items-center">
    <a-spin />
  </div>
</template>
