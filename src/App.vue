<script setup>


import { ref, computed } from "vue";
import axios from "axios";


const file = ref(null);

const fileName = computed(() => file.value?.name);
const fileExtension = computed(() => fileName.value?.substr(fileName.value?.lastIndexOf(".") + 1));
const fileMimeType = computed(() => file.value?.type);



const uploadFile = (event) => {
  const preview = document.querySelector("img");
  file.value = event.target.files[0];
  const reader = new FileReader();
  reader.readAsDataURL(file.value);

  reader.addEventListener(
    "load",
    () => {
      preview.src = reader.result;
    },
    false,
  );
};

const submitFile = async () => {
  const reader = new FileReader();
  reader.readAsDataURL(file.value);
  reader.onload = async () => {
    // console.log(reader.result); return;
    const encodedFile = reader.result.split(",")[1];
    // console.log(encodedFile); return;
    const data = {
      image: encodedFile,
      name: fileName.value,
      key: "7f6cc205efcbfcae5a75a2194c86ce64"
    };
    const config = {
      headers: {
        'Content-Type' : 'multipart/form-data; charset=UTF-8',
      }
    }
    try {
      const endpoint = "https://api.imgbb.com/1/upload?key=7f6cc205efcbfcae5a75a2194c86ce64";
      const response = await axios.post(endpoint, data, config);
      const imageResponse = response.data.data;
      
      var lists = JSON.parse(localStorage.getItem('image-list')) || [];
      var newResponse = {"imageUrl": imageResponse.image.url, "imageName": imageResponse.image.name};
      lists.push(newResponse);

      localStorage.setItem('image-list', JSON.stringify(lists));

      // notification here
    } catch (error) {
      console.error(error);
    } finally {
      const preview = document.querySelector("img");
      preview.src = null;
      
    }
  };
};


</script>

<template>
  <div class="bg-white dark:bg-slate-800 px-6 py-8 ring-1 ring-slate-900/5 shadow-xl grid place-content-center min-h-screen">
    
    

    <input
    type="file"
    style="display: block"
    class="mb-4 dark:text-white"
    ref="fileInput"
    accept="image/*"
    @change="uploadFile"/>

    <img src="" class="mt-2 mb-2" width="400" alt="" />
    
    <button class="rounded-md bg-indigo-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600" @click="submitFile">Upload profile picture</button>
      


    <h2 class="text-2xl font-bold tracking-tight text-gray-900 dark:text-gray-300 mt-5">List Picture</h2>
    <div class="mt-6 grid grid-cols-1 gap-x-6 gap-y-10 sm:grid-cols-2 lg:grid-cols-4 xl:gap-x-8">
      <div class="group relative">
        <div class="aspect-h-1 aspect-w-1 w-full overflow-hidden rounded-md bg-gray-200 lg:aspect-none group-hover:opacity-75 lg:h-80">
          <img src="https://tailwindui.com/img/ecommerce-images/product-page-01-related-product-01.jpg" alt="Front of men&#039;s Basic Tee in black." class="h-full w-full object-cover object-center lg:h-full lg:w-full">
        </div>
      </div>
    </div>
    
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
