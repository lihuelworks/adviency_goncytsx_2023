<script setup>
import CSSnowflakes from "./components/CSSnowflakes.vue";
let stuff = ["🧦 Medias", "🍬 Caramelos", "🐟 Vitel tone"]
</script>

<template>
  <div class="app">
    <div class="card">
      <h1>Regalos</h1>
      <ul>
        <li v-for="item in stuff">
          {{ item }}
        </li>
      </ul>
    </div>
    <CSSnowflakes class="snow" />
    <div class="background-image"></div>
  </div>
</template>

<style scoped lang="scss">
.app {
  /* height: 100vh; */
  /* color: oklch(10.79% 0.0026 237.54); */
  --app-bg: url('https://images.pexels.com/photos/91216/pexels-photo-91216.jpeg');
  display: grid;
  place-items: center;
  height: 100vh;

  background: oklch(10.79% 0.0026 237.54);
}

.background-image {
  z-index: 1;
  position: absolute;
  inset: 0;
  background: var(--app-bg);
  background-size: cover;
  background-position: center;
  transition: 0.3s ease-out;
  
  filter: blur(7px);

  &:after {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(to top, oklch(0% 0 0 / 25%), oklch(0% 0 0 / 0%));
  }
}

.snow {
  z-index: 10;
  position: absolute;
}

.card {
  position: relative;
  display: grid;
  row-gap: 1.5em;
  background-color: oklch(100 0 0);
  padding: 2em;
  z-index: 100;

  &>h1 {
    font-family: 'Mountains of Christmas', serif;
    font-weight: 700;
  }

  &>ul {
    list-style: none;
    padding: 0;
    line-height: 1.4;
    font-size: 1.2em;
    font-weight: 400;
    font-family: 'Kalam', cursive;
  }

}
</style>
