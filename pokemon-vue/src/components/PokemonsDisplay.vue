<template>
  <div>
    <div v-if="!pokemons">Loading Pokemons...</div>
    <div v-else>
      <div class="favButtonSearch"><button type="button" class="btn btn-primary" @click="displayFavs()">Search for Favs
        </button></div>
      <div class="allButtonSearch"><button type="button" class="btn btn-primary" @click="displayAll()">Search for
          All</button></div>
      <div class="teamButtonSearch"><button type="button" class="btn btn-primary" @click="displayTeam()">Search for
          team</button></div>
      <div class="dropdown">
        <button class="btn btn-primary dropdown-toggle" type="button" id="dropdownMenuButton" data-toggle="dropdown"
          aria-haspopup="true" aria-expanded="false">
          Types Select
        </button>
        <div class="dropdown-menu" aria-labelledby="dropdownMenuButton">
          <a class="dropdown-item" @click="displayTypes('fire')">Fire</a>
          <a class="dropdown-item" @click="displayTypes('water')">Water</a>
          <a class="dropdown-item" @click="displayTypes('grass')">Grass</a>
          <a class="dropdown-item" @click="displayTypes('flying')">Flying</a>
        </div>
      </div>
      <div class="rangeButtonSearch"><button type="button" class="btn btn-primary" @click="openSlideRange()">Search for Range</button></div>
      <div class="invButtonSearch"><button type="button" class="btn btn-primary" @click="openInventory()" >Show Inventary</button></div>
      <div v-if="getPokByRange">
        <SliderRange ref="SliderRange"></SliderRange>
      </div>
      <div v-if="showInv">
        <ShowInv ref="SliderRange"></ShowInv>
      </div>
      <div v-if="!fav && !teamSelect && !type && !getPokByRange && !showInv" class="cards">
        <div v-for="pokemon in pokemons" :key="pokemon.name" :class="getPokemonClasses(pokemon)" style="width: 18rem;">
          <h5 class="card-title showId" data-id="{{pokemon.id}}">Number:{{ pokemon.id }}</h5>
          <div :class="{ 'favIcon icons': !checkForFavs(pokemon), 'favIcon favIconCheck icons': checkForFavs(pokemon) }"
            @click="addToFav(pokemon)"> <img class="images" src="../assets/lockIcon.png" alt="" srcset=""> </div>
          <div
            :class="{ 'addTeamIcon icons': !checkForTeam(pokemon), 'addTeamIcon teamIconCheck icons': checkForTeam(pokemon) }"
            @click="addToTeam(pokemon)" />
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p>Types: {{ getPokemonTypes(pokemon) }}</p>
          </div>
        </div>
      </div>
      <div v-else-if="(fav && favPokemons.length > 0)" class="cards">
        <div v-for="pokemon in this.favPokemons" :key="pokemon.name" :class="getPokemonClasses(pokemon)"
          style="width: 18rem;">
          <div :class="{ 'favIcon icons': !checkForFavs(pokemon), 'favIcon favIconCheck icons': checkForFavs(pokemon) }"
            @click="addToFav(pokemon)"> <img class="images" src="../assets/lockIcon.png" alt="" srcset=""> </div>
          <h5 class="card-title">Number: {{ pokemon.id }}</h5>
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p>Types: {{ getPokemonTypes(pokemon) }}</p>
          </div>
        </div>
      </div>
      <div v-else-if="(teamSelect && team.length > 0)" class="cards">
        <div v-for="pokemon in this.team" :key="pokemon.name" :class="getPokemonClasses(pokemon)" style="width: 18rem;">
          <h5 class="card-title">Number: {{ pokemon.id }}</h5>
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p>Types: {{ getPokemonTypes(pokemon) }}</p>
          </div>
        </div>
      </div>
      <div v-else-if="type" class="cards">
        <div v-for="pokemon in this.choosedType" :key="pokemon.name" :class="getPokemonClasses(pokemon)"
          style="width: 18rem;">
          <div :class="{ 'favIcon icons': !checkForFavs(pokemon), 'favIcon favIconCheck icons': checkForFavs(pokemon) }"
            @click="addToFav(pokemon)"> <img class="images" src="../assets/lockIcon.png" alt="" srcset=""> </div>
          <div
            :class="{ 'addTeamIcon icons': !checkForTeam(pokemon), 'addTeamIcon teamIconCheck icons': checkForTeam(pokemon) }"
            @click="addToTeam(pokemon)" />
          <h5 class="card-title">Number: {{ pokemon.id }}</h5>
          <img class="card-img-top" :src="pokemon.sprites.front_default" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p>Types: {{ getPokemonTypes(pokemon) }}</p>
          </div>
        </div>
      </div>
      <div v-else-if="teamSelect && team.length === 0" class="emptyTeam">
        <h2>You haven't pokemons in your team</h2>
        <p>Make click in this icon to add pokemons.</p>
        <div class="addTeamIcon example"></div>
      </div>
      <div v-else-if="fav && favPokemons.length == 0">
        <h2>You haven't pokemons in favorites</h2>
        <p>Make click in this icon to add pokemons<img class="images" src="../assets/lockIcon.png" alt="" srcset=""></p>
      </div>
      <div class="limitExceded" id="limitExceded">You can have only {{ limitPokemonsTeam }} pokemons in your team</div>

    </div>

  </div>
</template>
<script>
import SliderRange from './SliderRange.vue';
import ShowInv from './ShowInventory.vue';
export default {
  components: {
    SliderRange,
    ShowInv,
  },
  data() {
    return {
      title: "PokemonsDisplay",
      pokemons: [],
      fav: false,
      favPokemons: [],
      checkFavs: null,
      team: [],
      teamSelect: false,
      limitPokemonsTeam: 6,
      choosedType: [],
      type: false,
      getPokByRange: false,
      showInv: false,
    };
  },
  methods: {
    fetchData() {
      fetch('https://pokeapi.co/api/v2/pokemon/?limit=151')
        .then(response => response.json())
        .then(data => {
          const pokemonPromises = data.results.map(pokemon => {
            return fetch(pokemon.url)
              .then(response => response.json());
          });
          // Promise para iterar sobre todos los datos
          Promise.all(pokemonPromises)
            .then(pokemonDataArray => {
              this.pokemons = pokemonDataArray;
            });
        });
    },
    checkForFavs(pokemon) {
      for (const i of this.favPokemons) {
        if (pokemon.name === i.name) {
          // this.checkFavs = true
          return true;
        } else {
          this.checkFavs = false
        }
      }
      return this.checkFavs
    },
    checkForTeam(pokemon) {
      for (const o of this.team) {
        if (pokemon.name === o.name) {
          return true;
        } else {
          this.teamSelect = false
        }
      }
      return this.teamSelect
    },
    // Remover el pokemon si ya existe en favPokemons.
    // Agregar el pokemon a favPokemons y aplicar la clase 'favIconCheck' al icono.
    addToFav(pokemon) {
      let index = this.favPokemons.findIndex(p => p.name === pokemon.name);
      if (index !== -1) this.favPokemons.splice(index, 1);
      else this.favPokemons.push(pokemon);
      
      // for (let i = 0; i < this.favPokemons.length; i++) {
      //   console.log(this.favPokemons[i]);
        
      // }

      let favIcon = document.querySelector('.fav Icon[data-id="' + pokemon.id + '"]');
      if (favIcon) {
        favIcon.classList.add('favIconCheck');
      }
    },
    addToTeam(pokemon) {
      let limitExceded = document.getElementById('limitExceded')
      let index = this.team.findIndex(p => p.name === pokemon.name);
      if (index !== -1 || this.team.length >= this.limitPokemonsTeam) this.team.splice(index, 1);
      else this.team.push(pokemon)

      if (this.team.length >= this.limitPokemonsTeam) {
        limitExceded.style.display = 'block'
        setTimeout(() => {
          limitExceded.style.display = 'none'
        }, 3000);
      }

      let teamIcon = document.querySelector('.addTeamIcon Icon[data-id="' + pokemon.id + '"]');
      if (teamIcon) teamIcon.classList.add('teamIconCheck');
    },
    openInventory() {
      this.resetDisplayFlags();
      this.showInv = true
    },
    openSlideRange() {
      if (this.$refs.SliderRange) {
        let div = this.$refs.SliderRange.div
        div.style.display = 'block'
      }
      this.resetDisplayFlags();
      this.getPokByRange = true;
    },
    displayFavs() {
      this.resetDisplayFlags();
      this.fav = true;
    },
    displayAll() {
      this.resetDisplayFlags();
    },
    displayTeam() {
      this.resetDisplayFlags();
      this.teamSelect = true;
    },
    // El filter está para crear una array con todos los pokemons que cumplan con la condición de que el typo sea igual al type selected, esto lo hace con el metodo some en cada tipo, que es para comprovar si algún elemento cumple con la condición, devolviento true o false, al contrario de filter, que creará un array.
    // Básicamente devuelve los pokemons que su tipo sea igual al typeSelected.  
    displayTypes(typeSelected) {
      this.resetDisplayFlags();
      this.type = true;
      this.choosedType = this.pokemons.filter(pokemon =>
        pokemon.types.some(type => type.type.name === typeSelected)
      );
    },
    resetDisplayFlags() {
      this.fav = this.teamSelect = this.type = this.getPokByRange = this.showInv = false;
    },
    getPokemonClasses(pokemon) {
      const types = pokemon.types.map(type => type.type.name);
      return {
        'fire card': types.includes('fire'),
        'aqua card': types.includes('water'),
        'grass card': types.includes('grass'),
        'poison card': types.includes('poison'),
        'electric card': types.includes('electric'),
        'ground card': types.includes('ground'),
        'psychic card': types.includes('psychic'),
        'dragon card': types.includes('dragon'),
        'normal card': types.includes('normal'),
        'bug card': types.includes('bug'),
        'fairy card': types.includes('fairy'),
        'fighting card': types.includes('fighting'),
        'aleix card': types.includes('hotdog'),
        'fly card': types.includes('flying')
      };
    },
    getPokemonTypes(pokemon) {
      return pokemon.types.map(type => type.type.name).join(', ');
    }
  },
  mounted() {
    this.fetchData();

  },
};

</script>
<style>
.limitExceded {
  display: none;
  padding: 10px;
  color: white;
  background-color: #010f23de;
  position: fixed;
  top: 280px;
  left: 39%;
  border-radius: 13px;
}

.cards {
  display: ruby;
}

.card {
  margin: 10px;
  padding: 10px;
  border-radius: 20px;
}

.favIconCheck {
  background-color: yellow !important;
}

.images {
  width: 30px;
}

.icons {
  background-color: grey;
  position: absolute;
  border-radius: 100%;
  width: 34px;
  height: 36px;
  transition: 0.5s;
}

.favIcon {
  right: 14px !important;
}

.addTeamIcon {
  background-color: unset;
  width: 42px;
  background-repeat: no-repeat;
  background-size: 37px;
  background-image: url('../assets/addToTeam.svg');
  left: 14px !important;
}

.example {
  width: 40px;
  height: 200px;
}

.emptyTeam {
  place-items: center;
  display: grid;
}

.teamIconCheck {
  background-image: url('../assets/team.png') !important;
}

.favIcon:hover,
.addTeamIcon:hover {
  cursor: pointer;
  transform: scale(1.05);
}

.dropdown-menu {
  top: -139px !important;
  left: 135px !important;
}

.dropdown {
  z-index: 0 !important;
}

.dropdown-toggle::after {
  transform: rotate(-88deg) !important;
}

.favButtonSearch,
.allButtonSearch,
.teamButtonSearch,
.rangeButtonSearch,
.invButtonSearch {
  top: 200px;
  position: absolute;
  z-index: 100;
}
.invButtonSearch {
  right: 510px;
}
.favButtonSearch {
  left: 80px;
}

.allButtonSearch {
  right: 80px;
}

.teamButtonSearch {
  right: 310px;

}

.rangeButtonSearch {
  left: 310px;
}
.grass {
  background-color: #a2dba2;
  border: 3px solid green;
}

.fire {
  background-color: #dba2a2;
  border: 3px solid red;
}

.aqua {
  background-color: #a2b9db;
  border: 3px solid blue;
}

.poison {
  background-color: #dba2d8;
  border: 3px solid rebeccapurple;
}

.electric {
  background-color: #dadba2;
  border: 3px solid yellow;
}

.ground {
  background-color: #a07f7f;
  border: 3px solid brown;
}

.psychic {
  background-color: #d087ec;
  border: 3px solid rgb(128, 0, 255);
}

.dragon {
  background-color: #dbb7a2;
  border: 3px solid #DE7339;
}

.normal {
  background-color: #b4b3b2;
  border: 3px solid #3f3f3f;
}

.bug {
  background-color: #76db62;
  border: 3px solid #2bff60;
}

.fairy {
  background-color: #d87cc9;
  border: 3px solid #ad26be;
}

.fly {
  background-color: #a9cce9;
  border: 3px solid #2b99ff;
}

.fighting {
  background-color: #dcf18d;
  border: 3px solid #d8ff2b;
}
</style>