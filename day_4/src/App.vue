<script setup>
import CSSnowflakes from './components/CSSnowflakes.vue'
import { ref } from 'vue'

const regalos = ref(["🍬 caramelo", "🐟 vitel tone", "🧦 medias"])

const new_regalo = ref("")

function addRegalo() {
  const value = regalos.value
  regalos.value = [new_regalo.value, ...value]
  new_regalo.value = ""
}

function removeRegalo(item) {
  let regalos_filtered = regalos.value.filter(i => i != item)
  regalos.value = regalos_filtered
}

</script>

<template>
  <div class="app">
    <div class="card">
      <h1 class="m-4">Regalos</h1>
      <input autofocus type="text" id="regalos-input" v-model="new_regalo" @keyup.enter="addRegalo" />
      <button @click="addRegalo" class="button">Añadir</button>
      <ul class="regalos-list">
        <li v-for="item in regalos" :key="item" class="regalos-item">
          {{ item }}
          <button @click="removeRegalo(item)">X</button>
        </li>
      </ul>
    </div>
    <CSSnowflakes class="snow"/>
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
  font-size: 1.2em;

}

button {
  border-radius: 0.6rem;
  border: 1px solid transparent;
  font-size: 1em;
  font-weight: 500;
  cursor: pointer;
  transition: border-color 0.25s;
  background-color: oklch(60.7% 0.2 274);
  color: white;

  &:hover {
    border-color: oklch(60.7% 0.15 274);
  }

  &:focus,
  &:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
  }
}





.card {
  z-index: 9999;
  position: absolute;
  display: grid;
  row-gap: 1.5em;
  background-color: oklch(100 0 0);
  padding: 2em;


  #regalos-add-button {
    padding: 0.6em 1.2em;

    &:hover {
      transform: scale(1.05);
      transition: 0.3s ease-out;
    }
  }

  &>h1 {
    font-family: 'Mountains of Christmas', serif;
    font-weight: 700;
    justify-self: center;
  }

  .regalos-list {
    list-style: none;
    padding: 0;
    line-height: 1.4;
    font-size: 1.2em;
    font-weight: 400;
    font-family: 'Kalam', cursive;


    .regalos-item {
      display: flex;
      justify-content: space-between;
      margin-bottom: 0.5em;
      & button {
        font-size: 0.5em;
        color: white;
        border-radius: 20px;
        &:hover {

        }
      }
    }

  }

  &:hover~.background-image {
    filter: blur(3px);
    will-change: transform, filter;
  }


}



.snow {
  z-index: 1;
  position: absolute;
}

.background-image {
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
</style>
