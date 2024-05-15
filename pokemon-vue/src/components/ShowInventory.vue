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
                <AddItems @amountGestion="inventoryGestion($event, item)"></AddItems>
            </div>
        </div>
    </div>
</template>
<script>
import AddItems from './AddAmountItems.vue'
export default{
    components: {
    AddItems,
    },
    data() {
        return {
          items: [],
          numMaxPokeballs: 15,
          numMaxPoc: 5,
          potions: [],
          childData: {},
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
                                // pokemonData.numPokeballs = pokemonData.name.includes('ball') ?  this.randomNumber(this.numMaxPokeballs) : this.randomNumber(this.numMaxPoc) ; this.potions.push(pokemonData.name)
                                if (pokemonData.name.includes('ball')) {
                                    pokemonData.numPokeballs = this.randomNumber(this.numMaxPokeballs);
                                } else {
                                    pokemonData.numPokeballs = this.randomNumber(this.numMaxPoc);
                                    this.potions.push(pokemonData.name);
                                }
                            });
                            this.items = pokemonDataArray;
                    });
                });
        },
        randomNumber(number) {
            return Math.floor(Math.random() * number)
        },
        inventoryGestion(addingItem, item) {
            let potiAdd = false
            this.potions.forEach(poti => {
                if ((item.name === poti ) ) potiAdd = true
            });
            console.log(this.potions);
            if (potiAdd && item.numPokeballs < this.numMaxPoc && (addingItem)) item.numPokeballs++
            else if(!potiAdd && item.numPokeballs < this.numMaxPokeballs && (addingItem)) item.numPokeballs++
            else if (item.numPokeballs > 0 && (!addingItem) ) {item.numPokeballs--; console.log('quitame');}
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