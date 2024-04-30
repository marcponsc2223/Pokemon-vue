<template>
    <div>{{console.log(this.items)}}</div>
</template>
<script>
export default{
    // el: '#application',
    data() {
        return {
          items: [],
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
                       
                        // Promise para iterar sobre todos los datos
                        Promise.all(pokemonPromises)
                            .then(pokemonDataArray => {
                                this.items = pokemonDataArray;
                            });
                    });
            },
        }
}
</script>
<style>

</style>