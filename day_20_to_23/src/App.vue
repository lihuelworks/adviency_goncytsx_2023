<script setup>
import { ref, watch, computed, onMounted  } from 'vue'
import CSSnowflakes from './components/CSSnowflakes.vue'
import { useFetch } from '@vueuse/core'

const url = "https://dummyjson.com/products"

const { isFetching, error, data } = useFetch(url)

const gift_storage = window.localStorage.getItem('gift_storage')
const gift_array = ref([
  { gift_name: "🍬 caramelo", gift_receiver: "Mama", gift_price: 15, img_url: "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png" },
  { gift_name: "🐟 vitel tone", gift_receiver: "Primito", gift_price: 200, img_url: "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png" },
  { gift_name: "🧦 medias", gift_receiver: "Abuela", gift_price: 300, img_url: "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png" }])

if (gift_storage !== null) {
  gift_array.value = JSON.parse(gift_storage)
}

const new_gift_name = ref()
const new_gift_price = ref(0)
const new_gift_img_url = ref()
const new_gift_receiver = ref()
const new_gift_amount = ref(1)
const dialog_visible = ref(false)
const dialog = ref(null);
const dialog_mode = ref("add")
const dialog_index = ref(0)
const mute = ref(false);
const image_url_regex_check = /\.(jpeg|jpg|gif|png|webp)$/i

const totalGiftPrice = computed(() => {
  return gift_array.value.reduce((accumulator, currentGift) => accumulator + currentGift.gift_price, 0);
});

/* const api = {
  gifts: {
    list()
  }
} */

watch(gift_array, (new_gift_storage) => {
  localStorage.setItem("gift_storage", JSON.stringify(new_gift_storage))
})

function randomGift() {
  const random_gift_list = [
    "🍬 caramelo",
    "🐟 vitel tone",
    "🧦 medias",
    "🎁 regalo",
    "📚 libro",
    "🌹 ramo de flores",
    "🍷 botella de vino",
    "🎉 fiesta sorpresa",
    "💄 maquillaje",
    "👕 camiseta personalizada"
  ];

  const random_gift_index = Math.floor(Math.random() * random_gift_list.length);

  new_gift_name.value = random_gift_list[random_gift_index];
}

function handleSubmit() {
  if (dialog_mode.value === "add") {
    addGift()
  } else if (dialog_mode.value === "edit") {
    editGift()
  } else {
    // console.log("ERROR! Invalid handleSubmit path reached!")
  }
}

function handlePrint() {
  window.print(); // This line triggers the print dialog
}

/* Handlers */

function openCloseDialog(mode, item, index) {

  if (mode === "edit") {
    dialog_mode.value = "edit"
    dialog_index.value = index

    new_gift_name.value = item.gift_name;
    new_gift_price.value = item.gift_price;
    new_gift_receiver.value = item.gift_receiver;
    new_gift_img_url.value = item.img_url;
  } else if (mode === "duplicate") {
    dialog_mode.value = "add"

    dialog_index.value = index

    new_gift_name.value = item.gift_name;
    new_gift_price.value = item.gift_price;
    new_gift_receiver.value = item.gift_receiver;
    new_gift_img_url.value = item.img_url;
  } else if (mode === "add") {
    dialog_mode.value = "add"
  } else if (mode === "preview") {
    dialog_mode.value = "preview"
  }

  if (!dialog?.value) return;
  else if (!dialog_visible.value) {
    dialog_visible.value = true
    dialog.value.showModal()
  }
  else {
    new_gift_name.value = ""
    new_gift_price.value = 0
    new_gift_amount.value = 1
    new_gift_img_url.value = ""
    new_gift_receiver.value = ""
    dialog_visible.value = false
    dialog.value.close()
  };
}

function addGift() {

  const value = gift_array.value
  if (new_gift_name.value) {
    if (!image_url_regex_check.test(new_gift_img_url.value)) {
      // console.log("Error loading image, using placeholder instead!")
      new_gift_img_url.value = "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png"
    }
    const new_gift_object = { gift_name: new_gift_name.value, gift_receiver: new_gift_receiver.value, gift_price: new_gift_price.value, img_url: new_gift_img_url.value }
    const new_gift_array = new Array(new_gift_amount.value).fill(new_gift_object)
    gift_array.value = [...new_gift_array, ...value]
  }

  openCloseDialog()
}

function editGift() {
  const new_gift_object = { gift_name: new_gift_name.value, gift_receiver: new_gift_receiver.value, gift_price: new_gift_price.value, img_url: new_gift_img_url.value }
  gift_array.value.splice(dialog_index.value, 1, new_gift_object)
  dialog_index.value = null
  openCloseDialog();

}

function removeGift(item_arg, index_arg) {
  const new_list = gift_array.value.filter((i, i_index) => {
    return i != item_arg || i_index != index_arg
  })
  gift_array.value = new_list
}

function removeAllGift() {
  gift_array.value = []
}

function toggleMute() {
  const audio = document.getElementById('audio');
  if (audio) {
    audio.muted = !audio.muted;
    mute.value = audio.muted;
  }
}

function playAudio() {
  console.log("AUDIO")
  const audio = document.getElementById('audio');
  if (audio) {
    audio.play();
  }
}
</script>

<template>
  <dialog class="dialog print" ref="dialog">
    <form v-if="dialog_mode !== 'preview'" name="gift-form" method="dialog">
      <h2>🎁 ¡Añadí tu regalo! 🎁</h2>
      <div class="dialog-new-gift-name-section">
        <label for="new_gift_name" class="dialog-new-gift-name-label">Añadí el nombre de tu regalo: </label>
        <input autofocus type="text" v-model="new_gift_name" name="new_gift_name" />
        <button type="button" @click="randomGift">¡Sorprendeme!</button>
      </div>

      <div class="dialog-new-gift-price-section">
        <label for="new_gift_price" class="dialog-new-gift-price-label">Añadí el precio de tu regalo: </label>
        <input autofocus type="number" v-model="new_gift_price" name="new_gift_price" />
      </div>

      <div class="dialog-new-gift-receiver-section">
        <label for="new_gift_receiver" class="dialog-new-gift-receiver-label">Añadí quien lo recibe: </label>
        <input autofocus type="text" v-model="new_gift_receiver" name="new_gift_receiver" />
      </div>

      <div class="dialog-new-gift-url-section">
        <label for="new_gift_img_url" class="dialog-new-gift-url-label">Añadí link a una imagen de tu regalo: </label>
        <input autofocus type="text" v-model="new_gift_img_url" name="new_gift_img_url" class="dialog-new-gift-img-url" />
      </div>

      <div class="dialog-new-gift-amount-section">
        <label for="new_gift_amount" class="dialog-new-gift-amount-label">Añadí el número de estos regalos: </label>
        <input type="number" name="new_gift_amount" id="new_gift_amount" min="1" placeholder="1"
          v-model="new_gift_amount">
      </div>

      <button @click="openCloseDialog" class="close-dialog-button">Cerrar</button>
      <button @click="handleSubmit" type="submit">{{ dialog_mode === 'add' ?
        "Añadir" : "Guardar" }}</button>
    </form>
    <div class="dialog-card print" v-else-if="dialog_mode === 'preview'">
      <h1 class="card-title print">Comprar</h1>
      <div class="print">
        <ul class="gift-list-preview print">
          <li v-for="(item, index) in gift_array" :id="`${item.gift_name}_${index}`" class="gift-list-preview-item print">
            <img :src="item.img_url" :alt="`Imagen de ${item.gift_name}`" class="gift-list-item-icon" />
            <p class="gift-list-item-name">{{ item.gift_name }} <span>- ${{ item.gift_price }}</span></p>
            <p class="gift-list-item-receiver">{{ item.gift_receiver }}</p>
          </li>
        </ul>
        <hr />
      </div>
      <button @click="openCloseDialog" class="gift-button gift-button-close">Cerrar</button>
      <button @click="handlePrint" class="gift-button gift-button-print">Imprimir</button>
    </div>
  </dialog>


  <div class="app" @click.once="playAudio">
    <div class="card">
      <div class="mute-button" @click="toggleMute">
    {{ mute ? '🔇' : '🔉' }}
  </div>
      <h1 class="card-title">Regalos</h1>
      <button @click="openCloseDialog('add')" class="gift-button-add">Añadir Regalo</button>
      <div v-if="error">{{ error }}</div>
      <div v-else-if="isFetching">
        Loading...
      </div>
      <div v-else>
        <div v-if="gift_array.length">
          <ul class="gift-list-main">
            <li v-for="(item, index) in gift_array" :id="`${item.gift_name}_${index}`" class="gift-list-main-item">
              <img :src="item.img_url" :alt="`Imagen de ${item.gift_name}`" class="gift-list-item-icon" />
              <p class="gift-list-item-name">{{ item.gift_name }} <span>- ${{ item.gift_price }}</span></p>
              <p class="gift-list-item-receiver">{{ item.gift_receiver }}</p>
              <button @click="openCloseDialog('duplicate', item, index)"
                class="gift-list-item-button gift-list-item-button-duplicate">📑</button>
              <button @click="openCloseDialog('edit', item, index)"
                class="gift-list-item-button gift-list-item-button-edit">📝</button>
              <button @click="removeGift(item, index)"
                class="gift-list-item-button gift-list-item-button-remove">❌</button>
            </li>
          </ul>
          <hr />
          <div class="gift-list-main-price-total">Total: ${{ totalGiftPrice }}</div>
          <button @click="removeAllGift" class="gift-button gift-button-all-remove">Borrar todo</button>
          <button @click="openCloseDialog('preview')" class="gift-button gift-button-all-preview">Previsualizar</button>
        </div>
        <p v-else>
          No hay regalos, agregá uno!
        </p>
      </div>
    </div>
  </div>
  <CSSnowflakes class="snow" />
  <div class="background-image"></div>
  <div>
    <audio
      ref="audio"
      src="/christmas_loop.mp3"
      loop
      volume="0.02"
      id="audio"
    ></audio>
  </div>
</template>

<style scoped lang="scss">
/* Common styles */

@mixin card-common {
  display: grid;
  row-gap: 2em;
  padding: 3em;
  max-width: 70vw;



  & &>p,
  hr {
    text-align: center;
    opacity: 80%;
  }

  hr {
    margin: 1.5em;
  }

  .card-title {
    font-family: 'Mountains of Christmas', serif;
    font-weight: 700;
    justify-self: center;
    margin-bottom: 0.5em;
    letter-spacing: var(--font-letterspacing-3);
  }

  .gift-button {
    width: 100%;
    padding: 0.5em;
    margin-top: 1em;
  }

  &:hover~.background-image {
    filter: blur(3px);
    will-change: transform, filter;
  }
}

@mixin gift-list-common {
  width: 100%;
  padding: 0;
  padding-inline: 2em;
  font-size: 1em;
  font-weight: 400;
  font-family: 'Kalam', cursive;
  line-height: 1.4;
  list-style: none;

  overflow-y: auto;
  min-height: 100%;
  max-height: 30vh;

  &>p,
  hr {
    text-align: center;
    opacity: 80%;
  }

  hr {
    margin: 1.5em;
  }

}

@mixin gift-list-item {
  display: grid;
  margin-bottom: 1em;
  justify-self: start;
  column-gap: 0.2em;
  max-width: 100%;

  .gift-list-item-icon {
    display: inline-block;
    width: auto;
    height: 1.2em;
    margin: 0;
    padding: 0;
    grid-area: gift-area-icon;
    aspect-ratio: 1 / 1;
  }


  .gift-list-item-name {
    grid-area: gift-area-name;
    text-align: start;
    max-width: 40ch;
    overflow-wrap: break-word;
  }

  .gift-list-item-receiver {
    grid-area: gift-area-receiver;
    font-size: 0.8em;
    color: gray;
  }

  .gift-list-item-button {
    width: 3em;
    height: min-content;
    justify-self: center;
    color: white;
    text-align: center;
    display: grid;
    place-content: center;
    padding-top: 0.25em;
    align-self: center;
  }


}

/* Container styles */

.mute-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  font-size: 24px;
  cursor: pointer;
}

.app {
  display: grid;
  place-items: center;
  background: oklch(10.79% 0.0026 237.54);
  transition: transform 0.3s ease-out;
  font-size: 1.2em;
  min-height: 100dvh;
}

dialog {
  margin: auto;
  outline-style: none;
  text-align: center;
  border: none;
  border-radius: 2em;
  box-shadow: var(--shadow-4);
  padding: 0;
  min-height: fit-content;


  &>form {
    display: grid;
    row-gap: 2em;
    outline: 0em;
    padding: 3em 2em;


    &>h2 {
      font-size: 2.5em;
      width: max-content;
    }

    & * input,
    & * button,
    &>input,
    &>button {
      border-radius: 1em;
      border-style: dashed;
      height: var(--size-fluid-4);
    }

    & label {
      justify-self: start;
      width: max-content;
    }


    &>div {
      display: grid;
      justify-content: space-between;
      grid-template-columns: repeat(2, 1fr);
      grid-template-rows: 1fr 2fr;
      column-gap: 2em;
      row-gap: 1em;

      &>label {
        grid-column: 1/-1;
      }

      &>input {
        grid-column: 1/-1;
      }
    }


    .dialog-new-gift-name-section {
      grid-template-columns: repeat(3, 1fr);

      &>label {
        grid-column: 1/-1;
      }

      /* Made exception because of button prescence */
      &>input {
        grid-column: span 2;

        &>button {
          grid-column: span 1;
        }
      }
    }


    .close-dialog-button {
      background-color: oklch(60.7% 0.15 315.67);
    }

    &>input:focus-visible,
    input:focus,
    input:focus-within {
      border: 0.2em solid cadetblue;
      outline: none;
    }
  }

  .dialog-card {
    @include card-common;
    border-radius: inherit;
    background-color: inherit;
    position: relative;
    z-index: auto;
    height: 100%;

    .gift-button {
      &-close {
        margin-top: 2em;
        background-color: oklch(60.7% 0.15 315.67)
      }

      &-print {
        margin: 0;
      }
    }
  }


  .gift-list-preview {
    @include gift-list-common;

    .gift-list-preview-item {
      @include gift-list-item;

      column-gap: 1em;
      grid-template-columns: 0.5fr minmax(max-content, 4fr) 1fr 1fr 1fr;

      grid-template-areas:
        "gift-area-icon gift-area-name gift-area-name gift-area-name gift-area-name"
        "gift-area-icon gift-area-receiver gift-area-receiver . .";

      .gift-list-item-icon {
        height: 2em;
        aspect-ratio: 1/1;
      }

      .gift-list-item-name {
        font-size: 1.5em;
      }

      .gift-list-item-receiver {
        text-align: start;
        font-size: 1.2em;
      }
    }

  }

  /* Dialog states styles */

  &[open] {
    animation: var(--animation-slide-in-up) forwards;
    animation-duration: 0.4s;
  }

  &[open]::backdrop {
    animation: backdrop-fade-in 0.7s ease-out forwards;
    animation-duration: 5s;
  }


  &::backdrop {
    backdrop-filter: blur(15px);
  }
}

.card {
  @include card-common;
  z-index: 10;
  background-color: var(--surface-1);
  border-radius: var(--radius-4);
  box-shadow: var(--inner-shadow-2);

  .gift-button-add {
    margin-top: -1em;
    font-size: 1.2em;
    padding: 0.6rem 0.2rem;
  }

  .gift-list-main {
    @include gift-list-common;

    &-item {
      @include gift-list-item;

      grid-template-columns: 0.5fr minmax(max-content, 4fr) 1fr 1fr 1fr;
      grid-template-areas:
        "gift-area-icon gift-area-name gift-area-button-edit gift-area-duplicate-button gift-area-button-remove"
        "gift-area-icon gift-area-receiver gift-area-button-edit gift-area-duplicate-button gift-area-button-remove"
      ;


      &-button-duplicate {
        grid-area: gift-area-duplicate-button;

      }

      &-button-edit {
        grid-area: gift-area-button-edit;

      }

      &-button-remove {
        grid-area: gift-area-button-remove;
      }

    }

    &-price-total {
      display: flex;
      place-content: center;
      place-items: center;
    }
  }

  .gift-button {
    &-all {
      &-remove {
        margin-top: 2em;
        background-color: oklch(60.7% 0.15 315.67)
      }

      &-preview {
        margin-top: 1.5em;
        background-color: oklch(60.7% 0.12 236.48324665797344)
      }
    }
  }
}

.snow {
  z-index: 1;
  position: absolute;
}

.background-image {
  // background: url('../public/festive-bg.jpeg');
  background: url('https://images.pexels.com/photos/91216/pexels-photo-91216.jpeg') no-repeat center center;
  z-index: 0;
  position: fixed;
  inset: 0;
  background-size: cover;
  transition: 0.6s ease-out;
  overflow: hidden;
  filter: blur(7px);


  /* Preserve aspet ratio */
  min-width: 100%;
  min-height: 100%;

  &:after {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(to top, oklch(0% 0 0 / 25%), oklch(0% 0 0 / 0%));
  }
}

@media print {
  :not(.print) {
    display: none;
  }

  .app {
    display: block;
    width: auto;
    height: auto;
    overflow: visible;
  }

  body {
    margin-left: 0 !important;
    display: block;
    width: auto;
    height: auto;
    overflow: visible;
  }

  dialog {
    border-radius: 0;
    color: black;
    box-shadow: none;
    padding: 0;
    display: block;
    width: auto;
    height: auto;
    overflow: visible;
  }

  .dialog-card.print {
    overflow: visible;
    padding: 0 2em;
    max-width: none;
    max-height: none;
    background-color: white;
    display: grid;

    .card-title.print {
      margin-top: 1em;
    }

    .gift-list-preview.print {
      max-height: none;
      display: block;




      .gift-list-preview-item.print {
        display: grid;
        justify-content: center;
        grid-template-columns: 1fr 2fr;
        grid-template-areas:
          "gift-area-icon gift-area-name gift-area-name gift-area-name gift-area-name"
          "gift-area-icon gift-area-receiver gift-area-receiver . .";


        &:nth-child(5n + 1) {
          page-break-before: always;
        }


        .gift-list-item-icon {
          align-self: center;
          justify-self: end;
          height: 2em;
        }

        .gift-list-item-name {
          display: block;
          font-size: 1.5em;
          page-break-before: auto;
          page-break-inside: avoid;

          span {
            display: inline-block;
            font-size: 0.8em;
          }
        }

        .gift-list-item-receiver {
          display: block;
          font-size: 1em;
          color: black;
          page-break-before: auto;
          page-break-inside: avoid;
        }
      }

    }
  }

}
</style>
