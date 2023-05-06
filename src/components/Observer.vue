<script setup>
import { ref , onMounted , defineEmits} from 'vue';

const observer = ref(null)
const root = ref(null)
const emits = defineEmits(['intersect'])

onMounted(() =>{
    observer.value = new IntersectionObserver(([entry])=>{
        if(entry && entry.isIntersecting){
            emits('intersect')
        }
    })
     observer.value.observe(root.value)
})

</script>


<template>
    <div ref="root" class="w-full flex justify-center p-5 mx-auto">
        <a-spin />
    </div>
</template>