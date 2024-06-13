<template>
  <div>
  <!-- <div :style="{ backgroundColor: getColor(pokType2) }"> -->
       <div :style="{ backgroundColor: getColor(pokType2[0]) }">
        <div id="cajaPoke" >
        <div class="search-container q-pa-md">
      
      <div class="q-gutter-md" style="max-width: 300px; margin: 0 auto">
        <q-input
          outlined
          v-model="pokeBuscar"
          label="ID del Pokémon o Nombre"
          :error="!!errorMsg"
          :error-message="errorMsg"
        />
        <button @click="traer()" class="search-button">Traer datos</button>
      </div>
    </div>

   
      <div id="resumen_pokemon" class="pokemon-container" v-if="pokeapp">
        <div class="pokemon-image">
          <h5 class="id">#{{ pokId }}</h5>
          <img :src="imgPokemon" alt="Imagen de {{ pokNombre }}" />
        </div>
      </div>

      <div id="infoPok" v-if="pokeapp">
        <h5 class="nombre">{{ pokNombre }}</h5>

        <h5>Altura: {{ pokAltura }}M</h5>
        <h5>Peso: {{ pokPeso }}k</h5>
        <h5>Elemento(s): 
          <button id="typos">
          <span :style="{ backgroundColor: getColor(pokType2[0]) }">{{ pokType2[0] }}</span> 
          <span :style="{ backgroundColor: getColor(pokType2[1]) }">{{ pokType2[1] }}</span>
        </button>
        </h5>
      </div>

      <div id="habilidades" v-if="pokeapp" class="habilidades-container">
        <div id="barras">
          <div class="q-pa-md habilidades-barras">
            <h6 class="litle">HABILIDADES</h6>
            <h6>HP {{ Hp }}</h6>
            <q-linear-progress :value="Hp / 100" class="q-mt-md" />

            <h6>Attack {{ Attack }}</h6>

            <q-linear-progress
              :value="Attack / 100"
              color="warning"
              class="q-mt-sm"
            />
            <h6>Defense {{ Defense }}</h6>

            <q-linear-progress
              :value="Defense / 100"
              color="secondary"
              class="q-mt-sm"
            />
            <h6>Special Attack {{ Special_Attack }}</h6>

            <q-linear-progress
              :value="Special_Attack / 100"
              rounded
              color="accent"
              class="q-mt-sm"
            />
            <h6>Special Defense {{ Special_Defense }}</h6>

            <q-linear-progress
              :value="Special_Defense / 100"
              rounded
              color="purple"
              track-color="orange"
              class="q-mt-sm"
            />
            <h6>Speed {{ Speed }}</h6>
            <q-linear-progress
              :value="Speed / 100"
              rounded
              color="negative"
              class="q-mt-sm"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>



<script setup>
import axios from "axios";
import { ref } from "vue";

let imgPokemon = ref("");
let pokeBuscar = ref("");
let pokId = ref("");
let pokNombre = ref("");
let pokAltura = ref("");
let pokPeso = ref("");
let pokType = ref("");
let pokType2 = ref("");

let pokeapp = ref(false);
let Hp = ref("");
let Attack = ref("");
let Special_Attack = ref("");
let Special_Defense = ref("");
let Defense = ref("");
let Speed = ref("");
let errorMsg = ref(""); // Propiedad reactiva para el mensaje de error


const coloresTipos = {
  normal: "#A8A878",
  fire: "#F08030",
  water: "#6890F0",
  grass: "#78C850",
  electric: "#F8D030",
  ice: "#98D8D8",
  fighting: "#C03028",
  poison: "#A040A0",
  ground: "#E0C068",
  flying: "#A890F0",
  psychic: "#F85888",
  bug: "#A8B820",
  rock: "#B8A038",
  ghost: "#705898",
  dragon: "#A29BFE",
  dark: "#705848",
  steel: "#B8B8D0",
};

function getColor(type) {
  if (Array.isArray(type)) {
    return coloresTipos[type[0]] || "white"; // Si no se encuentra el color, devolver blanco
  } else {
    return coloresTipos[type] || "white"; // Si no se encuentra el color, devolver blanco
  }
}



async function traer() {
  if (!pokeBuscar.value) {
    // Mostrar mensaje de error si no se ha ingresado un nombre o número
    errorMsg.value = "Por favor, ingresa un nombre o número de Pokémon";
    return;
  }
  try {
    let res = await axios.get(
      `https://pokeapi.co/api/v2/pokemon/${pokeBuscar.value}`
    );
    errorMsg.value = "";
    pokeapp.value = ref(true);
    imgPokemon.value = res.data.sprites.other["official-artwork"].front_default;
    pokNombre.value = res.data.name;
    pokId.value = res.data.id;
    pokAltura.value = res.data.height;
    pokPeso.value = res.data.weight;
    pokType.value = res.data.types
      .map((typeInfo) => typeInfo.type.name)
      .join(" , ");
    pokType2.value = res.data.types.map((typeInfo) => typeInfo.type.name);
    Hp.value = res.data.stats[0].base_stat;
    Attack.value = res.data.stats[1].base_stat;
    Special_Attack.value = res.data.stats[2].base_stat;
    Special_Defense.value = res.data.stats[3].base_stat;
    Defense.value = res.data.stats[4].base_stat;
    Speed.value = res.data.stats[5].base_stat;

    console.log(res.data.sprites.other["official-artwork"].front_default);
    console.log(res.data);
  } catch {
    console.log(error);
  }
}
</script>



<style scoped>

#cajaPoke {

  display: flex;
  flex-direction: column; /* Cambiar a columna para mejor adaptabilidad en pantallas pequeñas */
  gap: 10px;
  border: 5px solid #2c3e50;
  border-radius: 25px;
  justify-content: center;
  align-items: center;
  max-width: 100%; /* Asegura que la caja no exceda el ancho del contenedor */
}

#typos{
  border: none;
  /* padding: 5px 10px; */
  border-radius: 5px;
  row-gap: 2px;
  background-color: rgb(202, 198, 198);
  cursor: pointer;
}
#habilidades {
  font-size: 2rem;
  text-align: center; /* Centrar texto para mejor apariencia en pantallas pequeñas */
}

.search-button {
  background-color: #3d80d8;
  color: #3b4cca;
  border: none;
  padding: 0.5rem 1rem;
  /* margin-top: 1rem; */
  cursor: pointer;
  border-radius: 4px;
  /* font-size: 1rem; */
  transition: background-color 0.3s ease; /* Transición suave para hover */
}

.search-button:hover {
  background-color: #e6b800; /* Color de fondo al hacer hover */
}

.pokemon-image img {
  max-width: 100%; /* Asegurar que la imagen no exceda el contenedor */
  height: auto; /* Mantener la relación de aspecto */
  border-radius: 30%;
  /* margin: 10px ; */
}
#infoPok {
  border: 10px;
  border-radius: 40px;
}

.habilidades-barras h6 {
  text-align: left;
  margin: 0.1rem 0;
}
.litle{
  color:red
}
.q-linear-progress {
  width: 100%;
}

.id {
  display: inline-block;
  content: attr(data-id);
  position: absolute;
  top: 13%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 9rem;
  color: rgba(0, 0, 0, 0.1);
}

.nombre {
  color: #ff09c8;
  text-align: center; /* Centrar el nombre para mejor apariencia en pantallas pequeñas */
  /* font-size: 2.5rem; Ajustar tamaño de fuente para mejor legibilidad */
}

/* Media queries para responsividad */
@media (min-width: 600px) {
  #cajaPoke {
    flex-direction: row; /* Cambiar a fila en pantallas más grandes */
  height: 100vh;
  }

  .search-button {
    font-size: 1.1rem;
  }

  .nombre {
    font-size: 2rem;
  }
}
</style>

