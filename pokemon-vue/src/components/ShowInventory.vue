<template>
    <div class="title">
        <img src="../assets/pokemonBag.png" alt="" class="bagIcon">
        <h1 class="text">BAG</h1>
    </div>
    <div v-if="!items">Loading Items...</div>
    <div v-else class="cards">
        <div v-for="item in this.items" :key="item.name" class="card" style="width: 10rem">
            <h5 class="card-title">{{item.numPokeballs}}/{{this.potions.includes(item.name) ? this.numMaxPoc : this.numMaxPokeballs }}</h5>
            <!-- <h5 class="card-title">{{item.numPokeballs}} / {{this.numMaxPokeballs}}</h5> -->
            <img class="card-img-top" :src="item.sprites.default" alt="Card image cap">
            <div class="card-body">
                <h5 class="card-title">{{ item.name.replace("-", " ") }} </h5>
                <!-- <p>Amount: {{item.numPokeballs}} </p> -->
                <!-- <p>Types: {{ getPokemonTypes(pokemon) }}</p> -->
            </div>
        </div>
    </div>
</template>
<script>
export default{
    // el: '#application',
    data() {
        return {
          items: [],
          numMaxPokeballs: 15,
          numMaxPoc: 5,
          potions: ['potion', 'antidote', 'burn-heal', 'ice-heal']
        }
    },
    methods: {
        fetchData() {
            fetch('https://pokeapi.co/api/v2/item')
                .then(response => response.json())
                .then(data => {
                    const pokemonPromises = data.results.map(item => {
                        return fetch(item.url)
                            .then(response => response.json());
                    });
                    // Promise para iterar sobre todos los datos, con un campo extra en el objeto, que numPokeballs, es para añadir que las pokeballs tengan una cantidad específica y las pociones otras.
                    Promise.all(pokemonPromises)
                        .then(pokemonDataArray => {
                            pokemonDataArray.forEach(pokemonData => {
                                pokemonData.numPokeballs = this.potions.includes(pokemonData.name) ? this.randomNumber(this.numMaxPoc) : this.randomNumber(this.numMaxPokeballs)
                            });
                            this.items = pokemonDataArray;
                    });
                });
        },
        randomNumber(number) {
            return Math.floor(Math.random() * number)
        },
    },
    mounted() {
        this.fetchData();
    },
}
</script>
<style>
    .title {
        display: inline;
        padding: 20px;
    }
    .bagIcon {
        right: 90px;
        top: 67px;
        position: relative;
        margin: 25px;
        transform: rotate(-35deg);
        width: 46px;
    }
    .text {
        font-weight: 730;
    }
</style>