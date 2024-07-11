<template>
  <v-app>
    <v-main>
      <router-view></router-view>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
body {
  font-family: 'Poppins';
}
::-webkit-scrollbar {
  background: transparent;
  width: 7px;
}

::-webkit-scrollbar-track {
  background: transparent;
}

::-webkit-scrollbar-thumb {
  background: #b9b9b9;
  border-radius: 30px;
  transition: background 0.3s ease-in-out;
}
</style>
