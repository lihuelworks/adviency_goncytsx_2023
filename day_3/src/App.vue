<script setup>
import { ref } from 'vue'
import CSSnowflakes from "./components/CSSnowflakes.vue";

const regalos = ref(["🍬 caramelo", "🐟 vitel tone", "🧦 medias"])
const new_regalo = ref()

function addRegalo() {
  if (new_regalo.value === '') {
    return
  }
  regalos.value = [new_regalo.value, ...regalos.value]
  new_regalo.value = ''
}
</script>

<template>
  <div class="app">
    <div class="card">
      <h1>Regalos</h1>
      <input autofocus type="text" id="regalos-input" v-model="new_regalo" @keyup.enter="addRegalo" />
      <button @click="addRegalo">Añadir</button>
      <ul>
        <li v-for="item in regalos" :id="item">{{ item }}</li>
      </ul>
    </div>
    <CSSnowflakes />
    <div class="background-image"></div>
  </div>
</template>

<style scoped lang="scss">
.app {
  --app-bg: url('https://images.pexels.com/photos/91216/pexels-photo-91216.jpeg');
  display: grid;
  place-items: center;
  height: 100vh;

  background: oklch(10.79% 0.0026 237.54);
  transition: transform 0.3s ease-out;

}

.background-image {
  z-index: 1;
  position: absolute;
  inset: 0;
  background: var(--app-bg);
  background-size: cover;
  background-position: center;
  transition: 0.6s ease-out;
  overflow: hidden;

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
  position: absolute;
  display: grid;
  row-gap: 1.5em;
  background-color: oklch(100 0 0);
  padding: 2em;
  z-index: 100;


  &:hover {
    transform: scale(1.05);
    transition: 0.3s ease-out;

  }

  &:hover~.background-image {
    filter: blur(3px);
    will-change: transform, filter;
  }


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

button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  cursor: pointer;
  transition: border-color 0.25s;
  background-color: oklch(60.7% 0.2 274.6);

  &:hover {
    border-color: #646cff;
  }

  &:focus,
  &:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
  }
}
</style>
