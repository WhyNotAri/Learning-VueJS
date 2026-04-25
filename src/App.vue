<script setup>
import {ref} from 'vue';
import axios from "axios";

const title = ref('Hello, World!');
const name = ref('Ari');

const changeTitle = () => {
  title.value = 'Hello?';
}

const phone = ref('');
const message = ref('');

const createUrl = (phone, message) => {
  message = message.replaceAll(' ', '+');
  return `https://wa.me/${phone}/text=?${message}`;
}

/* ----- */

const pokemon = ref(null);
const pokemonName = ref('');
const cry = ref(null);
const searching = ref(false);
const found = ref('');
const getPokemon = async (pokemonName) => {
  searching.value = true;

  try {
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemonName.trim().toLowerCase()}`);
    pokemon.value = response.data;
    cry.value = response.data.cries.latest;
  }catch(error) {
    pokemon.value = null;
    cry.value = null;
    found.value = axios.isAxiosError(error) && error.response?.status === 404 ? 'Pokemon not found' : 'Error';
  }finally {
    searching.value = false;
  }
}

const playCry = () => {
  if (cry.value) {
    const audio = new Audio(cry.value)
    audio.play()
  }
}

</script>

<template>
  <h1>{{ title }}</h1>
  <button @click="changeTitle()">Change</button>

  <br><br>

  <!--- Whatsapp --->
  <div>
    <div>
      <label for="name">Name:</label>
      <input type="text" id="name" name= "name" placeholder="Enter your name" v-model="name">
      <p>Hello, {{ name }}</p>
    </div>

    <div>
      <form>
        <label for="phone">Phone:</label>
        <input type="tel" id="phone" name="phone" placeholder="Enter your phone number" v-model="phone">

        <label for="message">Content:</label>
        <input type="text" id="message" name="message" placeholder="Enter your message" v-model="message">
      </form>
      <p>{{ createUrl(phone, message) }}</p>
    </div>
  </div>

  <!--- Pokemon --->
  <div>
    <search>
      <input v-model="pokemonName" type="text" placeholder="Enter pokemon name...">
      <button @click="getPokemon(pokemonName)">Search Pokemon</button>
    </search>

    <div v-if="searching">
      <p>Searching your pokemon...</p>
    </div>

    <div v-else-if="pokemon">
      <h2>{{ pokemon.name }}</h2>

      <button @click="playCry">Listen</button>

      <img :src="pokemon.sprites.other['official-artwork'].front_default" :alt="pokemon.name">
    </div>

    <div v-else-if="found">
      <p>{{ found }}</p>
    </div>

    <div v-else>
      <p>Search your pokemon</p>
    </div>
  </div>
</template>

<style scoped>

</style>
