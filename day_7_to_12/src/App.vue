<script setup>
import { ref, watch } from 'vue'
import CSSnowflakes from './components/CSSnowflakes.vue'

const gift_storage = window.localStorage.getItem('gift_storage')
// const gift = ref(["🍬 caramelo", "🐟 vitel tone", "🧦 medias"])
const gift_array = ref([
  { gift_name: "🍬 caramelo", gift_receiver: "Mama", img_url: "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png" },
  { gift_name: "🐟 vitel tone", gift_receiver: "Primito", img_url: "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png" },
  { gift_name: "🧦 medias", gift_receiver: "Abuela", img_url: "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png" }])
if (gift_storage !== null) {
  gift_array.value = JSON.parse(gift_storage)
}

const new_gift_name = ref()
const new_gift_img_url = ref()
const new_gift_receiver = ref()
const new_gift_amount = ref(1)
const dialog_visible = ref(false)
const dialog = ref(null);

const image_url_regex_check = /\.(jpeg|jpg|gif|png|webp)$/i
watch(gift_array, (new_gift_storage) => {
  localStorage.setItem("gift_storage", JSON.stringify(new_gift_storage))
})

function addGift() {
  const value = gift_array.value
  if (new_gift_name.value) {
    if (!image_url_regex_check.test(new_gift_img_url.value)) {
      console.log("Error loading image, using placeholder instead!")
      new_gift_img_url.value = "https://em-content.zobj.net/source/microsoft-teams/363/wrapped-gift_1f381.png"
    }
    const new_gift_object = { gift_name: new_gift_name.value, gift_receiver: new_gift_receiver.value, img_url: new_gift_img_url.value }
    const new_gift_array = new Array(new_gift_amount.value).fill(new_gift_object)
    gift_array.value = [...new_gift_array, ...value]
  }

  openCloseDialog()
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



function openCloseDialog() {
  if (!dialog?.value) return;
  else if (!dialog_visible.value) {
    dialog_visible.value = true
    dialog.value.showModal()
  }
  else {
    new_gift_name.value = ""
    new_gift_amount.value = 1
    new_gift_img_url.value = ""
    new_gift_receiver.value = ""
    dialog_visible.value = false
    dialog.value.close()
  };
}

</script>

<template>
  <dialog class="dialog" ref="dialog" @click="openCloseDialog">
    <form name="gift-form" method="dialog" @keyup.enter="addGift"  @click.stop>
      <h2>🎁 ¡Añadí tu regalo! 🎁</h2>
      <input autofocus type="text" v-model="new_gift_name"  name="new_gift_name" placeholder="Añadí el nombre de tu regalo" />
      <input autofocus type="text" v-model="new_gift_receiver" name="new_gift_receiver" placeholder="Añadí quien lo recibe" />
      <input autofocus type="text" v-model="new_gift_img_url" name="new_gift_img_url" class="dialog-new-gift-img-url"
        placeholder="Añadí link a una imagen de tu regalo" />
      <label for="new_gift_amount"  class="dialog-new-gift-amount-label">Añadí el número de estos regalos: </label>
      <input type="number" name="new_gift_amount" id="new_gift_amount" min="1" placeholder="1" v-model="new_gift_amount">
      <button @click="openCloseDialog" class="close-dialog-button">Cerrar</button>
      <button @click="addGift" type="submit">Añadir</button>
    </form>
  </dialog>


  <div class="app">
    <div class="card">
      <h1 class="card-title">Regalos</h1>
      <button @click="openCloseDialog" class="add-gift-button">Añadir Regalo</button>

      <div v-if="gift_array.length">
        <ul class="gift-list">
          <li v-for="(item, index) in gift_array" :id="`${item.gift_name}_${index}`" class="gift-item">
            <img :src="item.img_url" :alt="`Imagen de ${item.gift_name}`" class="gift-item-icon" />
            <p class="gift-item-name">{{ item.gift_name }}</p>
            <p class="gift-item-receiver">{{ item.gift_receiver }}</p>
            <button @click="removeGift(item, index)" class="gift-item-remove-button">X</button>
          </li>
        </ul>
        <button @click="removeAllGift" class="remove-all-gift-button">Borrar todos</button>
      </div>
      <p v-else>
        No hay regalos, agregá uno!
      </p>
    </div>s
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
    width: clamp(20em, 20vw, 50vw);
    row-gap: 2em;
    outline: 0em;
    padding: 3em 2em;

    &>h2 {
      font-size: 1.6em;

    }

    &>input,
    &>button {
      padding: 1em;
      margin: 0 2em;
      border-radius: 1em;
      border-style: dashed;
    }

    .dialog-new-gift-img-url {
      padding-bottom: 2.1em;

      &::placeholder {
        font-size: 0.9em;
        text-wrap: pretty;
        padding-top: 0.2em;
        padding-bottom: 0.2em;
      }
    }

    .dialog-new-gift-amount-label {
      margin-bottom: -2em;
    }

    .close-dialog-button {
      margin-top: 2em;
      background-color: oklch(60.7% 0.15 315.67)
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
  width: clamp(20em, 20vw, 50vw);
  background-color: var(--surface-1);
  border-radius: var(--radius-4);
  box-shadow: var(--inner-shadow-2);

  &>p {
    text-align: center;
    opacity: 80%;
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
    line-height: 1.4;
    font-size: 1em;
    font-weight: 400;
    font-family: 'Kalam', cursive;

    .gift-item {
      display: grid;
      margin-bottom: 1em;
      justify-self: start;

      
      grid-template-columns: 0.5fr 2fr 1fr;
      grid-template-areas:
        "gift-area-icon gift-area-name gift-area-remove-button"
        "gift-area-icon gift-area-receiver gift-area-remove-button"
        ;

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

      .gift-item-remove-button {
        grid-area: gift-area-remove-button;

        width: 2.5em;
        height: 2.5em;
        font-size: 0.5em;
        justify-self: end;
        color: white;
        border-radius: var(--radius-5);
        text-align: center;
      }
    }



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
