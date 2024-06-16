<template>
  <v-card class="blog-card" :height="320" elevation="4">
    <v-card-subtitle>
      <div
        v-if="getCategories"
        class="d-inline-flex ga-2 py-4 pr-4 justify-center align-center"
      >
      <span class="head-dot" :style="{'background-color': getHeadColor(getCategories)}"></span>
        <!-- <span v-for="(category, index) in getCategories" :key="index"> -->
        {{ getCategories }}
        <!-- </span> -->
      </div>
    </v-card-subtitle>
    <v-card-item v-if="blogData" class="px-0 pt-0">
      <v-img
        v-if="blogData.post_thumbnail"
        :src="blogData.post_thumbnail.URL"
        :alt="`Image for ${blogData.title}`"
        :height="150"
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
        <template v-slot:error>
          <v-img
            src="https://user-images.githubusercontent.com/47315479/81145216-7fbd8700-8f7e-11ea-9d49-bd5fb4a888f1.png"
            cover
            :height="150"
          />
        </template>
      </v-img>
      <div class="pa-4 d-flex flex-column ga-3 blog-info">
        <span
          v-if="blogData.title"
          class="text-h6 font-weight-bold blog-title"
          v-html="blogData.title"
        ></span>
        <span
          v-if="blogData.date"
          class="text-subtitle-2 text-medium-emphasis"
          >{{ getFancyTime(blogData.date) }}</span
        >
      </div>
    </v-card-item>
  </v-card>
</template>
<script setup>
import { defineProps, computed } from "vue";
import moment from "moment";

const props = defineProps({
  blogData: {
    type: Object,
    required: true,
    default: () => ({}),
  },
});

let getCategories = computed(() => {
  return Object.keys(props.blogData.categories).length > 1
    ? Object.keys(props.blogData.categories)[0] + " ..."
    : Object.keys(props.blogData.categories)[0];
});

function getFancyTime(date) {
  // const seconds = Math.floor((new Date() - new Date(date)) / 1000);
  // const intervals = [
  //   Math.floor(seconds / 31536000), // years
  //   Math.floor(seconds / 2592000), // months
  //   Math.floor(seconds / 604800), // weeks
  //   Math.floor(seconds / 86400), // days
  //   Math.floor(seconds / 3600), // hours
  //   Math.floor(seconds / 60), // minutes
  //   seconds, // seconds
  // ];
  // const labels = ["year", "month", "week", "day", "hr", "minute", "second"];

  // for (let i = 0; i < intervals.length; i++) {
  //   if (intervals[i] > 0) {
  //     return `${intervals[i]} ${labels[i]}${intervals[i] !== 1 ? "s" : ""} ago`;
  //   }
  // }
  // return "just now";
  
  return moment(date).fromNow();

}

function getHeadColor(item) {
  let color = {
    "Tech" :"green",
    "Scam Alert" : "red",
    "Behind the Code": "purple",
    "App Features": "cyan"
  }
  return color[item] ?? "black"
}
</script>

<style scoped lang="scss">
.blog-title {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  -webkit-line-clamp: 2;
  height: 47px;
  line-height: 1.1;
}
.head-dot {
  border-radius: 50%; 
  height:12px;
  width: 12px;
  display: inline;
}
</style>
