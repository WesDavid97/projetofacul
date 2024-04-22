<script setup>
import { onMounted, reactive, ref, computed } from "vue";
import ListPokemons from "../components/ListPokemons.vue";
import CardPokemonSelected from "../components/CardPokemonSelected.vue";

let urlBaseSvg = ref("https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/")
let pokemons = reactive(ref([]));
let searchPokemonField = ref("")
let pokemonSelected = reactive(ref());
let loading = ref(false)
let offset = ref(0)
let limit = ref(1000)


onMounted(() => {
  fetchPokemons() // Call the function to fetch pokemons
})

const pokemonsFiltered = computed(() => {
  if(pokemons.value && searchPokemonField.value){
    return pokemons.value.filter(pokemon =>
      pokemon.name.toLowerCase().includes(searchPokemonField.value.toLowerCase())
    )
  }
  return pokemons.value;
})

const selectPokemon = async (pokemon) => {
  loading.value = true;
  await fetch(pokemon.url)
 .then(res => res.json())
 .then(res => pokemonSelected.value = res)
 .catch(err => alert(err))
 .finally(() => loading.value = false)

  console.log(pokemonSelected.value)
}

const fetchPokemons = async () => {
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit.value}&offset=${offset.value}`)
  const data = await response.json()
  pokemons.value = [...pokemons.value,...data.results.map(pokemon => ({
    id: pokemon.url.split('/')[6],
    name: pokemon.name,
    url: pokemon.url
  }))]
  offset.value += limit.value // Update the offset
}

const handleScroll = async () => {
  const scrollPosition = document.querySelector('.card-list').scrollTop + document.querySelector('.card-list').offsetHeight
  const scrollHeight = document.querySelector('.card-list').scrollHeight

  if (scrollPosition >= scrollHeight - 200) { // If the user has scrolled to the bottom
    await fetchPokemons() // Fetch more pokemons
  }
}

</script>

<template>
  <main>
    <div class="container text-body-secondary">
      

      <div class="row mt-4">
        <div class="col-sm-12 col-md-6">
          
         <CardPokemonSelected
         :name="pokemonSelected?.name"
         :img="pokemonSelected?.sprites.other.dream_world.front_default"
         :height="pokemonSelected?.height"         
         :xp="pokemonSelected?.base_experience"
         :loading="loading"
         :species="pokemonSelected?.types.map(m => m.type.name).join(', ')"
         :ab="pokemonSelected?.abilities.map(m => m.ability.name).join(', ')"
         :move="pokemonSelected?.moves.map(m => m.move.name).join(', ')"
         :game="pokemonSelected?.game_indices.map(m => m.game_index).join(', ')"
         />
        </div>
        <div class="col-sm-12 col-md-6">
          <div class="card card-list" @scroll.passive="handleScroll">
            <div class="card-body row">
              
              <div class="input-group mb-3">
                <label 
                hidden 
                for="searchPokemonField" 
                class="form-label">Search for Pokemon by name...
              </label>
                <input
                v-model="searchPokemonField"
                type="text" 
                class="form-control" 
                id="searchPokemonField" 
                placeholder="Search for Pokemon by name...">
              </div>

              <ListPokemons
            v-for="pokemon in pokemonsFiltered"
            :key="pokemon.name"
            :name="pokemon.name"
            :id="pokemon.id"
            :urlBaseSvg="urlBaseSvg + pokemon.url.split('/')[6] + '.svg'"
            @click="selectPokemon(pokemon)"
          />
        </div>
      </div>
    </div>
    </div>
    </div>
  </main>
</template>

<style scoped>
.card-list{
  max-height: 75vh;
  overflow-y: scroll;
  overflow-x: hidden;
}

</style>
