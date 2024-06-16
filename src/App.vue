<template>
  <v-app>
    <v-app-bar color="white" density="default" :elevation="0">
      <!-- scroll-behavior="hide" -->
      <v-app-bar-title>
        <RouterLink to="/">
          <v-img :width="150" cover src="./assets/logotype/truecaller.svg" />
        </RouterLink>
      </v-app-bar-title>
    </v-app-bar>
    <v-main>
      <router-view />
    </v-main>
    <v-footer color="#1A237E" class="py-6 footer">
      © True Software Scandinavia AB
    </v-footer>
  </v-app>
</template>

<script setup>
//
</script>
<style scoped>
.footer {
  padding: 1% 10% 1% 10%;
  max-height: 70px;
}
</style>
