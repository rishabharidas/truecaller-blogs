<template>
  <head>
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css"
      integrity="sha512-SnH5WK+bZxgPHs44uWIX+LLJAJ9/2PkPKZ5QiAj6Ta86w+fsb2TkcmfRyVX3pBnMFcV7oQPJkl9QevSCWr3W6A=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    />
  </head>
  <div class="blog-header">
    <v-img
      aspect-ratio="16/8"
      cover
      class="header-image"
      src="@/assets/images/header.jpg"
    />
    <span class="text-h4 text-md-h2 font-weight-bold heading"
      >The Truecaller Blog</span
    >
    <i class="fa-solid fa-chevron-down arrow"></i>
  </div>
  <div class="content-view d-flex flex-column">
    <span class="text-h4 text-md-h2 font-weight-bold">Latest articles</span>
    <div class="d-flex align-center categories">
      <v-select
        class=""
        v-model="filter"
        label="Select"
        :items="categories"
        item-value="slug"
        item-title="name"
        variant="outlined"
        bg-color="white"
        single-line
        hide-details
        attach
        elevate
        @update:model-value="callCategoryBlogs(1)"
      />
    </div>
    <Blogs ref="blogs" />
    <v-pagination
      v-model="pageNumber"
      class="mt-7"
      :length="25"
      :total-visible="4"
      rounded="circle"
      active-color="rgba(11, 73, 180, 3)"
      @update:model-value="callCategoryBlogs()"
    ></v-pagination>
  </div>
</template>

<script setup>
import axios from "axios";
import Blogs from "./Blogs.vue";
import { ref, onBeforeMount } from "vue";

let categories = ref([]);
let filter = ref("all");
let pageNumber = ref(1);
const blogs = ref(null);

// fetch the categories from the API before the component is mounted,
onBeforeMount(async () => {
  try {
    // Make an API request to get the categories from the WordPress site
    await axios
      .get(
        "https://public-api.wordpress.com/rest/v1.1/sites/107403796/categories"
      )
      .then((res) => {
        // Sort the categories alphabetically and add a custom "All categories" option
        let sortedCategories =
          [
            ...res.data?.categories,
            { name: "All categories", slug: "all" },
          ].sort((a, b) =>
            a.name.toLowerCase().localeCompare(b.name.toLowerCase())
          ) ?? [];
        // Update the categories value with the sorted categories
        categories.value = sortedCategories;
      });
  } catch (err) {
    console.log(err);
  }
});
// Function to fetch blogs based on the selected category and page number

function callCategoryBlogs(page = 0) {
  // If a specific page number is provided, update the page number state
  if (page) pageNumber.value = page;
  // If the blogs object is defined, call the getBlogs method with the filter and page number
  if (blogs.value) {
    blogs.value.getBlogs(filter.value, page ? page : pageNumber.value);
  }
}
</script>
<style lang="scss" scoped>
.content-view {
  padding: 5% 15% 3% 15%;
  gap: 28px;
  justify-content: center;
}
.blog-header {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
}
.heading {
  position: absolute;
  color: white;
}
.arrow {
  position: absolute;
  bottom: 28px;
  color: white;
  font-size: 23px;
}

.header-image {
  height: 700px;
}

@media (min-width: 2440px) {
  .content-view {
    padding: 3% 20% 3% 20%;
  }
}

@media (max-width: 450px) {
  .content-view {
    padding: 6% 3% 3% 3%;
    justify-content: center;
    gap: 18px;
  }
  .categories {
    width: auto;
  }
  .header-image {
    height: 500px;
  }
}
@media (min-width: 450px) {
  .categories {
    max-width: 305px;
  }
}
</style>
