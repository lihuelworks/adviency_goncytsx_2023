<script setup>
import { ref, watch, computed } from 'vue'
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


function openCloseDialog(mode, item, index) {

  if (mode === "edit") {
    dialog_mode.value = "edit"
    dialog_index.value = index

    new_gift_name.value = item.gift_name;
    new_gift_price.value = item.gift_price;
    new_gift_receiver.value = item.gift_receiver;
    new_gift_img_url.value = item.img_url;
  } else if (mode === "add") {
    dialog_mode.value = "add"
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



</script>

<template>
  <dialog class="dialog" ref="dialog">
    <form name="gift-form" method="dialog">
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
  </dialog>
  <div class="app">
    <div class="card">
      <h1 class="card-title">Regalos</h1>
      <button @click="openCloseDialog('add')" class="add-gift-button">Añadir Regalo</button>
      <div v-if="error">{{ error }}</div>
      <div v-else-if="isFetching">
        Loading...
      </div>
      <div v-else>
        <div v-if="gift_array.length">
          <ul class="gift-list">
            <li v-for="(item, index) in gift_array" :id="`${item.gift_name}_${index}`" class="gift-item">
              <img :src="item.img_url" :alt="`Imagen de ${item.gift_name}`" class="gift-item-icon" />
              <p class="gift-item-name">{{ item.gift_name }} <span>- ${{ item.gift_price }}</span></p>
              <p class="gift-item-receiver">{{ item.gift_receiver }}</p>
              <button @click="openCloseDialog('edit', item, index)" class="gift-item-edit-button">📝</button>
              <button @click="removeGift(item, index)" class="gift-item-remove-button">❌</button>
            </li>
            <hr />
            <div class="gift-list-price-total">Total: ${{ totalGiftPrice }}</div>
          </ul>
          <button @click="removeAllGift" class="remove-all-gift-button">Borrar todo</button>
        </div>
        <p v-else>
          No hay regalos, agregá uno!
        </p>
      </div>
    </div>
  </div>
  <CSSnowflakes class="snow" />
  <div class="background-image"></div>
</template>

<style scoped lang="scss">
.app {
  display: grid;
  place-items: center;
  height: 100vh;
  background: oklch(10.79% 0.0026 237.54);
  transition: transform 0.3s ease-out;
  font-size: 1.2em;

}

dialog {
  margin: auto;
  outline-style: none;
  text-align: center;
  border: none;
  border-radius: 2em;
  box-shadow: var(--shadow-4);
  padding: 0;



  &>form {
    display: grid;
    // width: clamp(20em, 20vw, 50vw);
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
  z-index: 10;
  position: absolute;
  display: grid;
  row-gap: 2em;
  padding: 3em;
  width: max-content;
  background-color: var(--surface-1);
  border-radius: var(--radius-4);
  box-shadow: var(--inner-shadow-2);

  &>p,
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


  .add-gift-button {
    margin-top: -1em;
    font-size: 1.2em;
    padding: 0.6rem 0.2rem;
  }

  .gift-list {
    list-style: none;
    padding: 0;
    width: max-content;
    padding-inline: 2em;
    line-height: 1.4;
    font-size: 1em;
    font-weight: 400;
    font-family: 'Kalam', cursive;

    .gift-item {
      display: grid;
      margin-bottom: 1em;
      justify-self: start;


      grid-template-columns: 0.5fr 2fr 1fr 1fr;
      grid-template-areas:
        "gift-area-icon gift-area-name gift-area-edit-button gift-area-remove-button"
        "gift-area-icon gift-area-receiver gift-area-edit-button gift-area-remove-button"
      ;
      column-gap: 0.2em;

      .gift-item-icon {
        display: inline-block;
        width: auto;
        height: 1.2em;
        margin: 0;
        padding: 0;
        grid-area: gift-area-icon;
        aspect-ratio: 1 / 1;
      }


      .gift-item-name {
        grid-area: gift-area-name;
        justify-self: start;
      }

      .gift-item-receiver {
        grid-area: gift-area-receiver;
        font-size: 0.8em;
        color: gray;
      }

      .gift-item-edit-button {
        grid-area: gift-area-edit-button;
        width: 3em;
        height: min-content;
        justify-self: end;
        color: white;
        text-align: center;
        display: grid;
        place-content: center;
        padding-top: 0.25em;
      }

      .gift-item-remove-button {
        grid-area: gift-area-remove-button;

        width: 3em;
        height: min-content;
        justify-self: end;
        color: white;
        text-align: center;
        display: grid;
        place-content: center;
        padding-top: 0.25em;
      }
    }



  }

  .gift-list-price-total {
    display: flex;
    place-content: center;
    place-items: center;
  }

  .remove-all-gift-button {
    padding: 0.5em;
    margin-top: 2em;
    width: 100%;
    background-color: oklch(60.7% 0.15 315.67)
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
  // background: url('../public/festive-bg.jpeg');
  background: url('https://images.pexels.com/photos/91216/pexels-photo-91216.jpeg');
  z-index: 0;
  position: absolute;
  inset: 0;
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
