<script setup>
let lista_regalos = ["🧦 Medias", "🍬 Caramelos", "🐟 Vitel Tone"]
</script>

<template>
  <div class="app">
    <h1>Regalos:</h1>
    <ul>
      <li v-for="item in lista_regalos">{{ item }}</li>
    </ul>
  </div>
</template>

<style scoped>
.app {
  height: 80vh;
  display: grid;
  /* place-items: center; */
  place-content: center;
  row-gap: 1.5rem;

}
.app > ul {
  justify-self: start;
  list-style: none;
  padding: 0;
}

</style>
