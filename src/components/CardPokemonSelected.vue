<script setup>
const pokemon = defineProps(["name", "xp", "height", "img", "loading", "species", "ab", "move", "game"])

</script>

<template>
    <div class="card CardPokemonSelected"
:class="loading? '' : 'animate__animated animate__flipInX'"
>
  <img 
  v-if="pokemon.name"
  :src="pokemon.img" 
  height="180"
  class="card-img-top pt-4" 
  :alt="pokemon.name">

  <img 
  v-else
  src="../assets/egg_pokemon.svg" 
  height="250"
  class="card-img-top pt-4" 
  alt="???">

  <div class="card-body">
    <h5 class="card-title text-center">{{ pokemon.name || '???' }}</h5>
    <hr>
    <div class="row">
      <section class="col">
        <strong>XP: </strong>
        <span>{{ pokemon.xp }}</span>
      </section>                
      <section class="col">
        <strong>Height: </strong>
        <span>{{ pokemon.height }}</span>
      </section> 
    </div>
    <hr>
    <div class="row">
      <section class="col">
        <strong>Type: </strong>
        <span>{{ pokemon.species }}</span>
      </section>  
      <section class="col">
        <strong>Skills: </strong>
        <span>{{ pokemon.ab }}</span>
      </section>  
    </div>
    <hr>
    <div class="row">            
      <section class="col">
        <strong>Attack Moves: </strong>
        <span>{{ pokemon.move }}</span>
      </section>         
    </div>
    <hr>
    <div class="row">            
      <section class="col">
        <strong>Games: </strong>
        <span>{{ pokemon.game }}</span>
      </section>         
    </div>
  </div>
</div>
</template>

<style scoped>

.CardPokemonSelected{
    height: 75vh 100%;
    margin-bottom: 1rem;
    background: rgb(255,255,255);
    background: linear-gradient(80deg, rgba(223, 221, 221, 0.65) 0%, rgba(247, 241, 241, 0.8) 36%, rgba(230, 228, 228, 0.65) 100%);
    }

</style>