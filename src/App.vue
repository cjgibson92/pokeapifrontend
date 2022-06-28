<template>
<div class="text-center">
  <img alt="pokeball" class="pokeheared" src="./assets/pokeball.png">
  </div>
  <Pokedex msg="Open the Pokedex to start"/>

</template>

<script>
import Pokedex from './components/Pokedex.vue'


export default {
  name: 'App',
  components: {
    Pokedex
  }
}
</script>
