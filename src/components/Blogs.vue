<template>
  <div class="blogs-viewer">
    <div v-for="blog in blogs" :key="blog.slug" class="blog-container">
      <RouterLink :to="`/${blog.slug}`" class="blog-route">
        <v-skeleton-loader
          v-if="loading"
          class="mx-auto border blog-card"
          type="image, article"
        />
        <BlogCard v-else :blog-data="blog" class="blog-card" />
      </RouterLink>
    </div>
  </div>
</template>
<script setup>
import BlogCard from "./BlogCard.vue";
import { onMounted, ref, defineExpose, defineProps } from "vue";
import axios from "axios";

let blogs = ref([]);
let loading = ref(false);

onMounted(async () => {
  loading.value = true;
  await getBlogs("", 1);
});

const props = defineProps({
  limit: {
    type: Number,
    required: false,
    default: 20,
  },
});

defineExpose({
  getBlogs,
});

async function getBlogs(filter, page) {
  loading.value = true;
  try {
    await axios
      .get(
        `https://public-api.wordpress.com/rest/v1.1/sites/107403796/posts/?fields=slug,categories,post_thumbnail,title,date&number=${
          props.limit
        }&page=${page}${filter && filter != "all" ? `&category=${filter}` : ""}`
      )
      .then((res) => {
        blogs.value = res.data?.posts ?? [];
      });
  } catch (err) {
    console.log(err);
  }
  loading.value = false;
}
</script>
<style lang="scss" scoped>
.blogs-viewer {
  display: flex;
  flex-wrap: wrap;
  gap: 1.8rem;
  // max-width: 2330px;
}

@media screen and (min-width: 588px) {
  .blogs-viewer {
    display: grid;
    gap: 1.8rem;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  }
}

.blog-container {
  width: 100%;
}

.blog-card {
  width: 100%;
}

.blog-route {
  text-decoration: none !important;
  width: 100%;
  display: flex;
}
</style>
