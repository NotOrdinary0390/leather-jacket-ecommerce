<template>
  <div>
    <h1 class="my-4 ml-4 text-sm">News</h1>
    <hr class="min-[600px]:mb-16" />
    <div v-if="loading" class="loader-container">
      <LoaderComponent />
    </div>
    <div v-else class="flex flex-wrap justify-center">
      <div v-for="post in posts" :key="post.id" class="card">
        <div class="div-img">
          <img
            :src="useRuntimeConfig().public.MEDIA_URL + post.image"
            :alt="post.image_meta"
            class="post-image"
          />
        </div>
        <p class="text-sm mt-2">
          {{ post.title }}
        </p>
        <p class="capitalize text-gray-400 text-xs">
          {{ formatNewsDateCustom(post.created_at) }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";

/*------------------------
Define reactive variables
-------------------------*/
const posts = ref([]);
const loading = ref(false);

/* --------------- 
  Define functions 
  -----------------*/

const formatNewsDateCustom = (dateString) => {
  const date = new Date(dateString);
  const month = date.toLocaleString("default", { month: "long" });
  const day = date.getDate();
  const year = date.getFullYear();
  return `${month} ${day}, ${year}`;
};

const loadNews = () => {
  loading.value = true; //  Set the loader during loading.
  const url = new URL(useRuntimeConfig().public.APP_URL + "/proxy/posts");
  const params = {
    "no-paginate": "",
  };

  Object.keys(params).forEach((key) =>
    url.searchParams.append(key, params[key])
  );
  axios
    .get(url.toString())
    .then((response) => {
      posts.value = response.data.data.data.sort((a, b) => {
        // Sort by created_at in descending order
        return new Date(b.created_at) - new Date(a.created_at);
      });
    })
    .catch((error) => {
      console.error("Error loading news:", error);
    })
    .finally(() => {
      loading.value = false; // Hides the loader after loading
    });
};

onMounted(() => {
  loadNews();
});

useHead({
  title: "News - Alessandra Grimoldi: Latest Updates on Fashion Trends",
  meta: [
    {
      name: "description",
      content:
        "Stay updated with the latest news and trends in the fashion world at Alessandra Grimoldi. Explore our news section for insights into luxury leather jackets and more.",
    },
    {
      name: "keywords",
      content:
        "News, Alessandra Grimoldi, Fashion Trends, Luxury Leather Jackets, Latest Updates, Fashion News",
    },
  ],
});
</script>

<style scoped>
* {
  box-sizing: border-box;
}
.card {
  max-width: fit-content !important;
  margin: 0 auto;
  padding-bottom: 36px;
}

.div-img {
  width: 100%;
  max-width: 550px;
  overflow: hidden;
}

.post-image {
  width: 100%;
  object-position: center;
}

/* media queries  */
@media screen and (min-width: 600px) {
  .card {
    width: 45%;
    margin: 0 0 10px 20px;
    padding-bottom: 23px;
  }
  .div-img {
    width: 100%;
  }
  .post-image {
    width: 100%;
  }
}

@media screen and (min-width: 1100px) {
  .card {
    width: 20%;
    margin: 0 0 10px 20px;
  }
  .div-img {
    width: 100%;
  }
  .post-image {
    width: 100%;
  }
}
</style>
