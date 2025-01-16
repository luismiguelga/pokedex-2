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
      <!-- <select id="opciones" name="tipo" v-model="tipoFiltrado">
        <option value="" disabled>Elige un tipo</option>
        <option value="">Todos</option>
        <option v-for="tipo in tipo_pk" :value="tipo" :key="tipo">{{ tipo }}</option>
      </select> -->
      <button id="btnmas" @click="cargarMasPokemones(50)">Ver mas+</button>
    </div>
    <div id="info2">
      <div v-if="mostrar" id="info2">
        <div id="tarjetas" v-for="(pokemon, index) in listaFiltradaDePokemon" :key="index">
          <div id="mostrarcosas">
            <img :src="pokemon.img" alt="">
            <h1 id="namei">#{{ pokemon.numero }}</h1>
            <h1 id="namei">{{ pokemon.nombre }}</h1>
            <div id="namei2">
              <h1 v-for="(item, index) in pokemon.tipo_pk" :key="index" :class="item.toLowerCase()">{{ item }}</h1>
            </div>
            <button id="verinfo" class="learn-more" @click="ObtenerUrlPokemon(pokemon)">
              <span class="circle" aria-hidden="true">
                <span class="icon arrow"></span>
              </span>
              <span class="button-text">Ver más</span>
            </button>
          </div>
        </div>
      </div>
      <div v-if="mostrardos">
        <div id="nuevacosa">
          <div class="name_img">
            <h3 id="infonombre">{{ nombre }}</h3>
            <img id="foton" :src="img" alt="">
          </div>
          <div id="nuevacosa2">
            <div>
              <p id="info">HP <input id="barrap-hp" type="text" v-model="hp"> {{ hp }}%</p>
              <p id="info">Ataque <input id="barrap-attack" type="text" v-model="attack"> {{ attack }}%</p>
              <p id="info">Defensa <input id="barrap" type="text" v-model="defense"> {{ defense }}%</p>
              <p id="info">Especial ataque <input id="barrap" type="text" v-model="specialattack"> {{ specialattack }}%
              </p>
              <p id="info">Especial defensa <input id="barrap" type="text" v-model="specialdefense"> {{ specialdefense
                }}%
              </p>
              <p id="info">Velocidad <input id="barrap" type="text" v-model="speed"> {{ speed }}%</p>
            </div>
            <div class="leftinfo">
              <div>
                <p id="info3" v-for="(item, index) in tipo_pk" :key="index" :class="item.toLowerCase()">{{ item }}</p>
              </div>
              <button id="volver" @click="Volver()">Volver👈</button>
            </div>

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
let tipoSeleccionado = ref('');
let tipoFiltrado = ref('');


function getPokemonCardClasses(pokemon) {
  const classes = pokemon.tipo_pk.map((tipo) => tipo.toLowerCase()) // Convierte los tipos a minúsculas
  return classes.join(' ')
}


//Para la barra de busqueda es necesario:
//1- que el imput tenga un v-model que contenga el contenido del nombre de la variable(3)
//2- El vector donde esta guardado los datos a buscar
//3- una variable en este caso "buscar"
//4- El computed en el import
//5- en el div donde se esten almacenando las cosas le cambiamos el nombre por la de esta funcion:
//5.1-<div id="tarjetas" v-for="(pokemon, index) in listaFiltradaDePokemon" :key="index">
//6- La funcion de abajo
const listaFiltradaDePokemon = computed(() => {
  return pokemonList.value.filter((pokemon) => {
    const nombreCoincide = pokemon.nombre.toLowerCase().includes(buscar.value.toLowerCase());
    const tipoCoincide = tipoFiltrado.value === '' || pokemon.tipo_pk.includes(tipoFiltrado.value);
    return nombreCoincide && tipoCoincide;
  });
});



//Para la barra de busqueda es necesario:
//1- que el imput tenga un v-model que contenga el contenido del nombre de la variable(3)
//2- El vector donde esta guardado los datos a buscar
//3- una variable en este caso "buscar"
//4- El computed en el import
//5- en el div donde se esten almacenando las cosas le cambiamos el nombre por la de esta funcion:
//5.1-<div id="tarjetas" v-for="(pokemon, index) in listaFiltradaDePokemon" :key="index">
//6- La funcion de abajo
// const listaFiltradaDePokemon = computed(() => {
//   return pokemonList.value.filter((pokemon) =>
//     pokemon.nombre.toLowerCase().includes(buscar.value.toLowerCase())
//   );
// });


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
  obtenerTiposPokemon();
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
//Hace que los tipos de pokemon se muestren segun los select
async function obtenerTiposPokemon() {
  try {
    const response = await axios.get('https://pokeapi.co/api/v2/type');
    tipo_pk.value = response.data.results.map((tipo) => tipo.name);

    // Agrega un console.log para verificar los tipos
    console.log(tipo_pk.value);
  } catch (error) {
    console.error('Error al obtener los tipos de Pokémon:', error);
  }
}

</script>


<style scoped>
.leftinfo {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  width: 81%;
}

#body {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-transform: capitalize;
}

img {
  /* border: 5px solid #ffffff; */
  border-radius: 20px;
  width: 200px;
  height: 200px;
  margin-top: 30px;
}

#opciones {
  position: relative;
  left: 28%;
  width: 130px;
  height: 50px;
  border-radius: 10px;
  border: 5px solid #000000;
  background-color: #53535f;
  color: white;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}

#opciones:hover {
  color: #000000;
  background-color: white;
}

#btnmas {
  width: 150px;
  height: 50px;
  border-radius: 10px;
  border: 5px solid black;
  margin-left: 32%;
  top: -5px;
  background-color: #53535f;
  color: white;
}

#btnmas:hover {
  color: #000000;
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
  border: 5px solid black;
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

#volver {
  width: 200px;
  height: 80px;
  color: rgb(0, 0, 0);
  border-radius: 10px;
  background-color: #53535f;
  color: white;
  font-size: 30px;
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
}

#infonombre {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  font-size: 10mm;
  color: rgb(0, 0, 0);
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

#info3 {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  font-size: 5mm;
  color: rgb(0, 0, 0);
  border: 5px solid rgb(0, 0, 0);
  border-radius: 5px;
  width: 200px;
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
  position: relative;
  top: -20px;
}

#namei2 {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  font-size: 13px;
  display: flex;
  /* Mostrar en línea */
  gap: 10px;
  /* Margen entre elementos */
}

#namei2 h1 {
  display: inline;
  margin-right: 10px;
  position: relative;
  top: -20px;

}

#nuevacosa {
  margin-top: 30px;
  background-color: #a970ff;
  border-radius: 30px;
  background-image: url(https://images-wixmp-ed30a86b8c4ca887773594c2.wixmp.com/f/7c1cfff2-db8d-44cd-8a13-cb2a4162fae6/dd2zouo-047f7b3f-b895-43ac-82a4-abb6ca12c52a.png?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1cm46YXBwOjdlMGQxODg5ODIyNjQzNzNhNWYwZDQxNWVhMGQyNmUwIiwiaXNzIjoidXJuOmFwcDo3ZTBkMTg4OTgyMjY0MzczYTVmMGQ0MTVlYTBkMjZlMCIsIm9iaiI6W1t7InBhdGgiOiJcL2ZcLzdjMWNmZmYyLWRiOGQtNDRjZC04YTEzLWNiMmE0MTYyZmFlNlwvZGQyem91by0wNDdmN2IzZi1iODk1LTQzYWMtODJhNC1hYmI2Y2ExMmM1MmEucG5nIn1dXSwiYXVkIjpbInVybjpzZXJ2aWNlOmZpbGUuZG93bmxvYWQiXX0.EFI6YQ0acRLpYHzupMAxV_N4KMcpNWT-_3RJ4TV-4eU);
  background-size: cover;
  background-repeat: no-repeat;
  width: 1200px;
  height: 550px;
  margin-top: 30px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  border-radius: 30px;

}

#nuevacosa2 {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: space-around;
}

#foton {
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
  margin-top: 10px;
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

.name_img {
  display: flex;
  flex-direction: column;
  align-items: center;
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
  top: -5px;
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
}



.grass {
  background-color: rgb(1, 223, 1);
  border-radius: 5px;
}

.fire {
  background-color: red;
  border-radius: 5px;
}

.water {
  background-color: rgb(0, 132, 255);
  border-radius: 5px;
}

.poison {
  background-color: #9d00ff;
  border-radius: 5px;
}

.bug {
  background-color: green;
  border-radius: 5px;
}

.flying {
  border-radius: 5px;
  background-color: #53535fb6;
}

.normal {
  border-radius: 5px;
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.637);
}

.electric {
  border-radius: 5px;
  background-color: rgba(255, 255, 0, 0.74);
}

.ground {
  border-radius: 5px;
  background-color: rgb(131, 131, 0);
}

.fairy {
  border-radius: 5px;
  background-color: rgb(136, 0, 163);
}

.fighting {
  border-radius: 5px;
  background-color: rgb(255, 115, 0);
}

.psychic {
  border-radius: 5px;
  background-color: rgb(234, 0, 255);
}

.rock {
  border-radius: 5px;
  background-color: rgb(122, 73, 33);
}

.steel {
  border-radius: 5px;
  background-color: rgb(97, 97, 97);
}

.ice {
  border-radius: 5px;
  background-color: rgb(0, 183, 255);
}

.ghost {
  border-radius: 5px;
  background-color: rgba(0, 0, 0, 0.507);
}
</style>
