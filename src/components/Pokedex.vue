<template>
  <div class="wrapper">
    <h1 class="text-center">{{ msg }}</h1>
    <div class="row">
      <div class="col-md-3">
        <img v-if="!pokedexOpen" alt="pokedexclosed" @click="openPokedex" class="pokedex" src="../assets/pokedex.jpg">
        <img v-if="pokedexOpen" alt="pokedexopen" @click="closePokedex" class="pokedex" src="../assets/openPokedex.png">
        <LoadingBar v-if="loading"/>
      </div>
    <div class="col-md-9">
    <div v-if="pokedexOpen" class="row">
     <div class="col-2" 
          v-for="pokemon in pokedexInfo"
          :key="pokemon?.id"
          @click="getspecific(pokemon.id)"
          >
          <div class="row">
            <div class="col-md-6">
              <div class="row">
                <span>{{pokemon.name}}</span>
              </div>
              <div class="row">
                <span class="text-left">Type:</span>
              </div>
              <div class="row">
                <ul class="text-right small-font">
                  <li v-for="types in pokemon.types"
                  :key="types?.id">
                    {{types}}
                  </li>
                </ul>
              </div>
              <div class="row">
                <span class="text-left">Weight:</span>
              </div>
              <div class="row">
                <span class="text-right small-font">{{pokemon.weight}}</span>
              </div>
              <div class="row">
                <span class="text-left">Abilities:</span>
              </div>
              <div class="row">
                <ul class="text-right small-font">
                  <li v-for="abilities in pokemon.abilities"
                  :key="abilities?.name">
                    {{abilities.name}}
                  </li>
                </ul>
              </div>
            </div>
            <div class="col-md-6">
            <img class="margin-mid pointerclass" :src="pokemon.sprite">
            </div>
          </div>
         
        </div>
        </div>
         </div>
        <div v-if="pokedexOpen" class="row">
            <div class="col-4 offset-3 text-right">
                <img src="../assets/arrow.jpg" class="arrow previous" v-if="offset!=0" @click="backPage">
            </div>
            <div  class="col-4 offset-1">
              <img src="../assets/arrow.jpg" class="arrow next" v-if="offset!=999" @click="nextPage">
            </div>
    </div>
    <div v-if="showSpecific && pokedexOpen" class="row">
            <div class="col-md-2 offset-md-5">
              <div class="row">
                <h3>{{pokedexSpecific.name}}</h3>
              </div>
              <div class="row margin-top-10">
                <h5 class="margin-left-15">Type:</h5>
              </div>
              <div class="row">
                <ul class="margin-left-15">
                  <li v-for="types in pokedexSpecific.types"
                  :key="types?.id">
                    {{types}}
                  </li>
                </ul>
              </div>
              <div class="row margin-top-10">
                <h5 class="margin-left-15">Weight:</h5>
              </div>
              <div class="row">
                <span class="margin-left-15">{{pokedexSpecific.weight}}</span>
              </div>
              <div class="row margin-top-10">
                <h5 class="margin-left-15">Abilities:</h5>
              </div>
              <div class="row">
                <ul class="margin-left-15">
                  <li v-for="abilities in pokedexSpecific.abilities"
                  :key="abilities?.name">
                    {{abilities.name}}
                  </li>
                </ul>
              </div>
            </div>
            <div class="col-md-2">
            <img class="bigger" :src="pokedexSpecific.sprite"> 
        </div>
        <div class="col-md-3">
        <div class="row">
          <h3>Evolution Chain</h3>
        </div>
        <div class="row">
        <ul class="margin-left-15">
                  <li v-for="evolution in pokedexSpecific.evolucionChain"
                  :key="evolution?.id" 
                  @click="getspecific(evolution.id)">
                    <img class="pointerclass" :src="evolution.sprite">
                  </li>
                </ul>
        </div>
    </div>
    </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { onMounted } from "@vue/runtime-core";
import { ref } from "@vue/reactivity";
import LoadingBar from "./LoadingBar";
export default {
  name: 'PokedexComp',
  props: {
    msg: String
  },
  components:{
    LoadingBar
  },
  setup() {
    let timeout = null;
    clearTimeout(timeout);
    const pokedexInfo = ref("");
    const pokedexSpecific = ref("");
    const offset =ref(0);
    const showSpecific = ref(false);
    const loading =ref(false);
    const getPokemon = async () =>{
      loading.value=true;
      timeout = setTimeout(async() => {
        await axios.get(
           "https://project2-1656302484892.azurewebsites.net/getPokedex?offset="+offset.value,
            {
              offset: 0,
            },
            {
              headers: {
                  'Access-Control-Allow-Origin' : '*',
                  'Access-Control-Allow-Methods':'GET,PUT,POST,DELETE,PATCH,OPTIONS',
              },
              responseType: "json",
            }
          )
          .then((res) => {
            pokedexInfo.value = res.data;
            loading.value=false;
           })
          .catch((e) => {
            console.log(e)
            loading.value=false;
          });
      }, 500);
  }

  const getspecific = async (pokemonId) =>{
    loading.value=true;
      timeout = setTimeout(async() => {
        await axios.get(
           "https://project2-1656302484892.azurewebsites.net/getSpecifics?pokemonId="+pokemonId,
            {
              offset: 0,
            },
            {
              headers: {
                  'Access-Control-Allow-Origin' : '*',
                  'Access-Control-Allow-Methods':'GET,PUT,POST,DELETE,PATCH,OPTIONS',
              },
              responseType: "json",
            }
          )
          .then((res) => {
            pokedexSpecific.value = res.data;
            showSpecific.value = true;
            loading.value=false;
           })
          .catch((e) => { 
            console.log(e)
            loading.value=false;
             });
      }, 500);
  }
  const backPage = async () =>{
    offset.value = offset.value -12;
    if(offset.value <0){
      offset.value = 0;
    }
    getPokemon();
  }
   const nextPage = async () =>{
    offset.value = offset.value +12;
    getPokemon();
  }
  onMounted(getPokemon())
  return {getPokemon, pokedexInfo,nextPage,backPage,offset, getspecific,showSpecific,pokedexSpecific,loading};
  },
  data(){
    return {
      pokedexOpen: false,
    };
  },
  methods: {
    openPokedex() {
      this.pokedexOpen = !this.pokedexOpen;
    },
    closePokedex() {
      this.pokedexOpen = !this.pokedexOpen;
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
