<script setup>
const pokemon = defineProps(["id", "name", "urlBaseSvg"])

</script>

<template>
    <div class="col-4">
        <div class="card p-2 mb-3 cardListPokemon">
            <p class="text-center">#{{ pokemon.id }}  {{ pokemon.name }}</p>
            <img 
            :src="urlBaseSvg" class="card-img-top" 
            alt="..."
            height="80"
            />
        </div>
    </div>
</template>

<style scoped>
.cardListPokemon {
    background: rgb(255,255,255);
    background: radial-gradient(circle, rgba(255,255,255,0.6) 0%, rgba(231,249,255,0.4) 36%, rgba(169, 178, 218, 0.2) 100%);
    cursor: pointer;
}
.cardListPokemon:hover {
    background: rgb(255,255,255);
    background: radial-gradient(circle, rgba(255,255,255,0.6) 0%, rgba(231,249,255,0.7) 36%, hsla(229, 100%, 79%, 0.5) 100%);
    cursor: pointer;
}
.cardListPokemon img:hover {
    padding: .1rem;
}

</style>