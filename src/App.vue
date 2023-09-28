<template>
  <div id="body">
    <div id="info1">
      <h1 id="titulo">Pokedex</h1>
      <img id="pokeball"
        src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/51/Pokebola-pokeball-png-0.png/601px-Pokebola-pokeball-png-0.png"
        alt="">
      <div class="container">
        <input v-model="buscar" type="text" name="text" class="input" placeholder="Búsqueda en Dark Twitch">
        <button class="search__btn">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="22" height="22">
            <path
              d="M18.031 16.6168L22.3137 20.8995L20.8995 22.3137L16.6168 18.031C15.0769 19.263 13.124 20 11 20C6.032 20 2 15.968 2 11C2 6.032 6.032 2 11 2C15.968 2 20 6.032 20 11C20 13.124 19.263 15.0769 18.031 16.6168ZM16.0247 15.8748C17.2475 14.6146 18 12.8956 18 11C18 7.1325 14.8675 4 11 4C7.1325 4 4 7.1325 4 11C4 14.8675 7.1325 18 11 18C12.8956 18 14.6146 17.2475 15.8748 16.0247L16.0247 15.8748Z"
              fill="#efeff1"></path>
          </svg>
        </button>
      </div>
      <button id="btnmas" @click="cargarMasPokemones(50)">Ver mas+</button>
    </div>
    <div id="info2">
      <div v-if="mostrar" id="info2">
        <div id="tarjetas" v-for="(pokemon, index) in listaFiltradaDePokemon" :key="index">
          <div id="mostrarcosas">
            <img :src="pokemon.img" alt="">
            <h1 id="namei">#{{ pokemon.numero }}</h1>
            <h1 id="namei">{{ pokemon.nombre }}</h1>
            <h1 id="namei" v-for="(item, index) in tipo_pk" :key="index">{{ item }}</h1>
            <button id="verinfo" class="learn-more" @click="ObtenerUrlPokemon(pokemon)">
              <span class="circle" aria-hidden="true">
                <span class="icon arrow"></span>
              </span>
              <span class="button-text">Ver mas</span>
            </button>
          </div>
        </div>
      </div>
      <div v-if="mostrardos">
        <div id="nuevacosa">
          <h3 id="infonombre">{{ nombre }}</h3>
          <img id="foton" :src="img" alt="">
          <div id="nuevacosa2">
            <p id="info">HP <input id="barrap" type="text"> {{ hp }} </p>
            <p id="info">Ataque <input id="barrap" type="text">{{ attack }}</p>
            <p id="info">Defensa <input id="barrap" type="text">{{ defense }}</p>
            <p id="info">Especial ataque <input id="barrap" type="text">{{ specialattack }}</p>
            <p id="info">Espacial defensa <input id="barrap" type="text">{{ specialdefense }}</p>
            <p id="info">velocidad <input id="barrap" type="text">{{ speed }}</p>
            <p id="info">Tipo {{ tipo_pk }}</p>
            <div id="botones"><button id="volver" @click="Volver()">Volver👈</button></div>
          </div>
        </div>
      </div>
      
    </div>
  </div>
</template> 

<script setup>
import axios from "axios"
import { ref, onMounted, computed } from 'vue'

let img = ref('')
let numero = ref('')
let nombre = ref('')
let hp = ref('')
let attack = ref('')
let defense = ref('')
let specialattack = ref('')
let specialdefense = ref('')
let speed = ref('')
let tipo_pk = ref([])
let mostrar = ref(true)
let mostrardos = ref(false)
let pokemonList = ref([]);
let buscar = ref('');


//Para la barra de busqueda es necesario:
//1- que el imput tenga un v-model que contenga el contenido del nombre de la variable(3)
//2- El vector donde esta guardado los datos a buscar
//3- una variable en este caso "buscar"
//4- El computed en el import
//5- en el div donde se esten almacenando las cosas le cambiamos el nombre por la de esta funcion:
//5.1-<div id="tarjetas" v-for="(pokemon, index) in listaFiltradaDePokemon" :key="index">
//6- La funcion de abajo
const listaFiltradaDePokemon = computed(() => {
  return pokemonList.value.filter((pokemon) =>
    pokemon.nombre.toLowerCase().includes(buscar.value.toLowerCase())
  );
});

//Obtener la informacion de cada
async function ObtenerUrlPokemon(pokemon) {
  const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemon.numero}`)
  const data = response.data
  img.value = data.sprites.other["official-artwork"].front_default
  numero.value = data.id
  nombre.value = data.name
  hp.value = data.stats[0].base_stat
  attack.value = data.stats[1].base_stat
  defense.value = data.stats[2].base_stat
  specialattack.value = data.stats[3].base_stat
  specialdefense.value = data.stats[4].base_stat
  speed.value = data.stats[5].base_stat
  tipo_pk.value = data.types.map((element) => element.type.name)
  mostrar.value = false
  mostrardos.value = true
}

function Volver() {
  img.value = '';
  numero.value = '';
  nombre.value = '';
  hp.value = '';
  attack.value = '';
  defense.value = '';
  specialattack.value = '';
  specialdefense.value = '';
  speed.value = '';
  tipo_pk.value = [];
  mostrar.value = true;
  mostrardos.value = false;
}

onMounted(() => {
  obtenerPokemones()
})
//Cargar los primeros 50 inciales
async function obtenerPokemones() {
  for (let i = 1; i <= 50; i++) {
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}`)
    const data = response.data

    pokemonList.value.push({
      img: data.sprites.other["official-artwork"].front_default,
      numero: data.id,
      nombre: data.name,
      hp: data.stats[0].base_stat,
      attack: data.stats[1].base_stat,
      defense: data.stats[2].base_stat,
      specialattack: data.stats[3].base_stat,
      specialdefense: data.stats[4].base_stat,
      speed: data.stats[5].base_stat,
      tipo_pk: data.types.map((element) => element.type.name),
    })
  }
}
//Cargar mas de los 50 inicales 
async function cargarMasPokemones(cantidad) {
  const startIndex = pokemonList.value.length + 1;
  const endIndex = startIndex + cantidad - 1;

  for (let i = startIndex; i <= endIndex; i++) {
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}`)
    const data = response.data

    pokemonList.value.push({
      img: data.sprites.other["official-artwork"].front_default,
      numero: data.id,
      nombre: data.name,
      hp: data.stats[0].base_stat,
      attack: data.stats[1].base_stat,
      defense: data.stats[2].base_stat,
      specialattack: data.stats[3].base_stat,
      specialdefense: data.stats[4].base_stat,
      speed: data.stats[5].base_stat,
      tipo_pk: data.types.map((element) => element.type.name),
    })
  }
}

</script>


<style scoped>
#body {
  display: flex;
  flex-direction: column;
  align-items: center;
}

img {
  /* border: 5px solid #ffffff; */
  border-radius: 20px;
  width: 200px;
  height: 200px;
  margin-top: 30px;
}

#btnmas{
width: 150px;
height: 50px;
border-radius: 10px;
border: 5px solid black;
position: relative;
left: 40%;
top: -15px;
background-color: #53535f;
color: white;
}

#btnmas:hover{color: #000000;

  background-color: white;
}
#buscar {
  height: 30px;
  width: 300px;
}
#botones {
  width: 200px;
  height: 70px;
  display: flex;
  justify-content: center; 
  align-items: start;
  background-color: #9700ce; 
  border: none; 
  border-radius: 5px; 
  font-size: 40px; 
  text-decoration: none; 
  cursor: pointer; 
  position: relative;
  left: 300px;
  top: 100px;
}
#botones:hover {
  background-color: #9d00ff; 
}


#volver{
position: relative;
top: -23px;
color:rgb(0, 0, 0)
}

#pokeball {
  width: 80px;
  height: 80px;
  position: relative;
  left: 150px;
  top: -15px;
}

#info {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  font-size: 5mm;
  color: rgb(0, 0, 0);
  position: relative;
  top: 37px;
  left: 160px;
}

#infonombre {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  font-size: 10mm;
  color: rgb(0, 0, 0);
  position: relative;
  top: 15px;
  left: 190px;
}

#info1 {
  width: 100%;
  background-color: rgb(0, 153, 255);
  display: flex;
  align-items: center;
}

#info2 {
  display: flex;
  flex-wrap: wrap;
  gap: 30px;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

#tarjetas {
  margin-top: 50px;
  background-color: rgba(90, 189, 255, 0.726);
  width: 300px;
  height: 450px;
  color: rgb(0, 0, 0);
  border-radius: 30px;
  border: 5px solid #000000;
}

#tarjetas h1,
#tarjetas p {
  margin: -15px 0px;
  margin-top: 30px;
}

#mostrarcosas {
  display: flex;
  flex-direction: column;
  justify-content: end;
  align-items: center;
}

#titulo {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  position: relative;
  left: 100px;
}

#namei {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

#nuevacosa {
  margin-top: 30px;
  display: flex;
  background-color: #a970ff;
  border-radius: 30px;
  background-image: url(https://images-wixmp-ed30a86b8c4ca887773594c2.wixmp.com/f/8168a26e-e2d0-454e-9239-267f073715cf/dd777on-b4242a08-5757-401c-8e51-0dca0a438469.png/v1/fill/w_1280,h_854/fakemon_template___1_stage_pokemon_by_gecko_comics_dd777on-fullview.png?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1cm46YXBwOjdlMGQxODg5ODIyNjQzNzNhNWYwZDQxNWVhMGQyNmUwIiwiaXNzIjoidXJuOmFwcDo3ZTBkMTg4OTgyMjY0MzczYTVmMGQ0MTVlYTBkMjZlMCIsIm9iaiI6W1t7ImhlaWdodCI6Ijw9ODU0IiwicGF0aCI6IlwvZlwvODE2OGEyNmUtZTJkMC00NTRlLTkyMzktMjY3ZjA3MzcxNWNmXC9kZDc3N29uLWI0MjQyYTA4LTU3NTctNDAxYy04ZTUxLTBkY2EwYTQzODQ2OS5wbmciLCJ3aWR0aCI6Ijw9MTI4MCJ9XV0sImF1ZCI6WyJ1cm46c2VydmljZTppbWFnZS5vcGVyYXRpb25zIl19.5EMy2p0mB6ZqcgqAdpC9KYXpNr7XFG8xUgwyGxTFS40);
  background-size: cover;
  background-repeat: no-repeat;
  width: 1200px;
  height: 550px;
}

#nuevacosa2 {
  position: relative;
  left: 50px;
}

#foton {
  position: relative;
  top: 150px;
  left: -110px;
  width: 300px;
  height: 300px;
}

















button {
  position: relative;
  display: inline-block;
  cursor: pointer;
  outline: none;
  border: 0;
  vertical-align: middle;
  text-decoration: none;
  background: transparent;
  padding: 0;
  font-size: inherit;
  font-family: inherit;
  margin-top: 30px;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

button.learn-more {
  width: 12rem;
  height: auto;
}

button.learn-more .circle {
  transition: all 0.45s cubic-bezier(0.65, 0, 0.076, 1);
  position: relative;
  display: block;
  margin: 0;
  width: 3rem;
  height: 3rem;
  background: #9900ffec;
  border-radius: 1.625rem;
}

button.learn-more .circle .icon {
  transition: all 0.45s cubic-bezier(0.65, 0, 0.076, 1);
  position: absolute;
  top: 0;
  bottom: 0;
  margin: auto;
  background: #fff;
}

button.learn-more .circle .icon.arrow {
  transition: all 0.45s cubic-bezier(0.65, 0, 0.076, 1);
  left: 0.625rem;
  width: 1.125rem;
  height: 0.125rem;
  background: none;
}

button.learn-more .circle .icon.arrow::before {
  position: absolute;
  content: "";
  top: -0.29rem;
  right: 0.0625rem;
  width: 0.625rem;
  height: 0.625rem;
  border-top: 0.125rem solid #000000;
  border-right: 0.125rem solid #000000;
  transform: rotate(45deg);
}

button.learn-more .button-text {
  transition: all 0.45s cubic-bezier(0.65, 0, 0.076, 1);
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  padding: 0.75rem 0;
  margin: 0 0 0 1.85rem;
  color: #ffffff;
  font-weight: 700;
  line-height: 1.6;
  text-align: center;
  text-transform: uppercase;
}

button:hover .circle {
  width: 100%;
}

button:hover .circle .icon.arrow {
  background: #000000;
  transform: translate(1rem, 0);
}

button:hover .button-text {
  color: #000000;
}


.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 35px;
  position: relative;
  left: 24%;
}

.input {
  width: 250%;
  height: 70%;
  outline: none;
  font-size: 14px;
  font-weight: 500;
  background-color: #53535f;
  caret-color: #f7f7f8;
  color: #fff;
  padding: 7px 10px;
  border: 2px solid transparent;
  border-top-left-radius: 7px;
  border-bottom-left-radius: 7px;
  margin-right: 1px;
  transition: all .2s ease;
}

.input:hover {
  border: 2px solid rgba(255, 255, 255, 0.16);
}

.input:focus {
  border: 2px solid #a970ff;
  background-color: #0e0e10;
}

.search__btn {
  border: none;
  cursor: pointer;
  position: relative;
  top: -15px;
  background-color: rgba(42, 42, 45, 1);
  border-top-right-radius: 7px;
  border-bottom-right-radius: 7px;
  height: 120%;
  width: 300px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.search__btn:hover {
  background-color: rgba(54, 54, 56, 1);
}</style>
