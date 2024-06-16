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
  <div v-if="blogDetails">
    <v-img
      v-if="blogDetails.featured_image"
      :src="blogDetails.featured_image"
      :max-height="700"
      cover
    >
      <template v-slot:placeholder>
        <div class="d-flex align-center justify-center fill-height">
          <v-progress-circular
            color="grey-lighten-4"
            indeterminate
          ></v-progress-circular>
        </div>
      </template>
    </v-img>
    <div class="d-flex flex-column ga-4 mt-4 blog-content">
      <span
        v-if="blogDetails.title"
        class="text-h3 font-weight-bold"
        v-html="blogDetails.title"
      ></span>
      <div v-if="blogDetails.author" class="d-flex ga-2 author-details">
        <v-img
          v-if="blogDetails.author.avatar_URL"
          :src="blogDetails.author.avatar_URL"
          :max-width="50"
          rounded="circle"
        />
        <div class="d-flex flex-column ga-1 justify-center">
          <span class="text-body-2 font-weight-medium">{{
            blogDetails.author.name
          }}</span>
          <span class="text-body-2 text-medium-emphasis">{{
            getFormattedDate(blogDetails.date)
          }}</span>
        </div>
      </div>
      <div v-html="blogDetails.content" class="px-1 blog-data"></div>
      <div class="other-blogs d-flex flex-column ga-4 my-8">
        <div class="d-flex justify-space-between">
          <span class="text-h3 text-font-bold">Other Blogs</span>
          <div class="ga-6 align-center d-none d-sm-flex">
            <i class="fa-solid fa-arrow-left" @click="scroll('left')"></i>
            <i class="fa-solid fa-arrow-right" @click="scroll('right')"></i>
          </div>
        </div>
        <div class="scrollable-blogs">
          <Blogs :limit="4" class="scroll-blogs" id="scrollBlogs" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, onUpdated, ref } from "vue";
import { useRoute } from "vue-router";
import axios from "axios";
import moment from "moment";

import Blogs from "@/components/Blogs.vue";

const route = useRoute();
const blogDetails = ref(null);

// Fetch blog details when the component is mounted
onMounted(() => {
  getblogDetails();
});

// Update the document title when the component is updated
onUpdated(() => {
  document.title =
    blogDetails.value && blogDetails.value.title
      ? `${blogDetails.value.title} | TrueCaller Blog`
      : "TrueCaller Blog";
});

/**
 * Scrolls the element with ID "scrollBlogs" horizontally.
 *
 * @param {string} towards - The direction to scroll. Accepts "right" or "left".
 */
function scroll(towards) {
  if (towards === "right")
    document.getElementById("scrollBlogs").scrollLeft += 80;
  if (towards === "left")
    document.getElementById("scrollBlogs").scrollLeft -= 80;
}

/**
 * Formats a given date into "MMM Do YYYY" format.
 *
 * @param {string|Date} date - The date to be formatted.
 * @returns {string} - The formatted date string.
 */
function getFormattedDate(date) {
  return moment(date).format("MMM Do YYYY");
}

/**
 * Fetches blog details from the WordPress API based on the current route's blog slug.
 *
 * @returns {Promise<void>} - A promise that resolves when the API call is complete.
 */
async function getblogDetails() {
  try {
    await axios
      .get(
        `https://public-api.wordpress.com/rest/v1.1/sites/107403796/posts/slug:${route.params.blog}?fields=featured_image,title,author,content,date`
      )
      .then((res) => {
        blogDetails.value = res.data;
      });
  } catch (err) {
    console.log(err);
  }
}
</script>
<style scoped lang="scss">
.blog-content {
  padding: 2% 20% 2% 20%;
}
@media (max-width: 450px) {
  .blog-content {
    padding: 2% 10% 2% 10%;
  }
}
.author-details {
  .avatar {
    border-radius: 50%;
  }
}
</style>
<style lang="scss">
.scrollable-blogs {
  .scroll-blogs {
    grid-template-columns: repeat(4, minmax(260px, 1fr));
    overflow-x: scroll;
    -ms-overflow-style: none;
    scrollbar-width: none;
    padding-bottom: 1rem;
    padding-top: 1rem;
  }
  /* Hide scrollbar for Chrome, Safari and Opera */
  .scroll-blogs::-webkit-scrollbar {
    display: none;
  }
}
.blog-data {
  font-size: 16px;
  line-height: 26px;
  max-width: none;
  white-space: normal;
  word-break: keep-all;
  p {
    margin-bottom: 1rem;
  }
  .wp-block-heading {
    line-height: 30px;
    margin-bottom: 1rem;
    font-size: 20px;
    font-weight: 700;
    max-width: none;
    white-space: normal;
    word-break: keep-all;
    letter-spacing: -0.5px;
  }
  .wp-block-list {
    margin-left: 20px;
    font-weight: 500;
    font-size: large;
    margin-bottom: 30px;
  }
  .wp-block-image {
    display: flex;
    flex-direction: column;
    gap: 0.3rem;
    width: 100%;
    justify-content: center;
    align-content: center;
    margin-bottom: 1rem;
    img {
      width: -webkit-fill-available;
      height: min-content;
    }
    figcaption {
      font-size: small;
    }
  }
  .wp-block-separator {
    margin-top: 2rem;
    margin-bottom: 2rem;
  }

  .wp-block-verse {
    text-wrap: balance;
    display: flex;
    flex-direction: column;
    justify-content: center;
    text-align: center;
    em {
      text-wrap: balance;
    }
  }
  .wp-block-button {
    display: flex;
    justify-content: center;
    margin-top: 2rem;
    margin-bottom: 2rem;
    a {
      --tw-bg-opacity: 1;
      text-decoration: none !important;
      background-color: rgba(0, 135, 255, var(--tw-bg-opacity));
      border-radius: 9999px;
      display: inline-block;
      font-weight: 600;
      font-size: 0.75rem;
      line-height: 1.125;
      line-height: 1;
      padding-left: 1.5rem;
      padding-right: 1.5rem;
      padding-top: 1.5rem;
      padding-bottom: 1.5rem;
      text-align: center;
      --tw-text-opacity: 1;
      color: rgba(255, 255, 255, var(--tw-text-opacity));
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
  }
}
</style>
