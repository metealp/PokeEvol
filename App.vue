<template>
    <div>
        <pokemon-cards 
        :pokemons="starters" 
        :selectedId="selectedId"
        @pokemonClicked="fetchEvolutions"
        />

        <pokemon-cards 
        :pokemons="evolutions" 
        @pokemonClicked="fetchEvolutions"
        />
    </div>

</template>

<script>
import Card from './Card.vue';
import PokemonCards from './PokemonCards.vue'
const api = "http://pokeapi.co/api/v2/pokemon"

const STARTER_IDS = [1, 4, 7]

export default {
    components: {
        Card,
        PokemonCards
    },
    data(){
        return {
            starters: [],
            evolutions: [],
            selectedId: null,
            
        }
    },
    async created(){
        const starters = await this.fetchData(STARTER_IDS)
        this.starters = starters
    },
    mounted(){
        
    },
    methods: {
        async fetchData(ids){
            const responses = await Promise.all(ids.map(id => window.fetch(`${api}/${id}`)));

            const data = await Promise.all(responses.map(response => response.json()))
            const savedData = data.map(datum => ({
                id: datum.id,
                name: datum.name,
                sprite: datum.sprites.other['official-artwork'].front_default,
                types: datum.types.map(item => ({name: item.type.name}))
            }));

            return savedData
        },

        async fetchEvolutions(pokemon){
            this.evolutions = await this.fetchData([pokemon.id + 1, pokemon.id + 2])
            this.selectedId = pokemon.id
        }
    },
}
</script>

<style scoped>
    
</style>