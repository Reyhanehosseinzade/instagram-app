<script setup>
import { ref, defineProps } from "vue";
import { supabase } from "../supabase";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
const userStore = useUserStore();
const { user } = storeToRefs(userStore);
const props = defineProps(["addNewPost"]);
const loading = ref(false);
const errorMessage = ref("");
const visible = ref(false);
const caption = ref("");
const file = ref(null);
const showModal = () => {
  visible.value = true;
};
const handleOk = async () => {
  loading.value = true;
  const fileName = Math.floor(Math.random() * 100000000000000000);
  let filePath;
  if (file.value) {
    const { data, error } = await supabase.storage
      .from("images")
      .upload("public/" + fileName, file.value);
    if (error) {
      loading.value = false;
      return (errorMessage.value = "Unable to Upload image");
    }
    filePath = data.path
    await supabase.from("posts").insert({
      url: filePath,
      owner_id: user.value.id,
      caption: caption.value,
    });
  }
  loading.value = false;
  visible.value = false;
  caption.value = "";
  props.addNewPost({
    url: filePath,
    caption: caption.value,
  });
};

const handleUploadChange = (e) => {
  if (e.target.files[0]) {
    file.value = e.target.files[0];
  }
};
</script>

<template>
  <div>
    <a-button @click="showModal">Upload photo</a-button>
    <a-modal
      v-model:visible="visible"
      centered
      title="Upload Photo"
      @ok="handleOk"
    >
      <div v-if="!loading">
        <input
          class="mb-2"
          type="file"
          accept=".jpeg,.png,.jpg"
          @change="handleUploadChange"
        />
        <a-input v-model:value="caption" placeholder="caption..."></a-input>
        <a-typography-Text v-if="errorMessage" type="danger">{{
          errorMessage
        }}</a-typography-Text>
      </div>
      <div class="w-full flex justify-center items-center h-[80px]" v-else>
        <a-spin />
      </div>
    </a-modal>
  </div>
</template>
