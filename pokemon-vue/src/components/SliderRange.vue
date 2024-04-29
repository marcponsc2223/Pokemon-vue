<template>
    <div id="application">
        <!-- <div class="slider-container">
          <input type="range" v-model="sliderValue" min="0" max="100" class="slider">
          <p>Valor mínimo: <span>{{ sliderValue }}</span></p>
          <p>Valor máximo: <span>{{ 100 - sliderValue }}</span></p>
        </div> -->

        <input type="range" v-model="minValue" :min="0" :max="100"  class="slider">
        <input type="range" v-model="maxValue" :min="0" :max="100" class="slider">
        <p>Valor mínimo: <span>{{ minValue }}</span></p>
        <p>Valor máximo: <span>{{ maxValue }}</span></p>
  
    </div>
</template>
<script>
// import Vue from 'vue';
export default({
    el: '#application',
    data() {
        return {
            minValue: 50,
            maxValue: 100,
            // sliderValue: 50,
        }
    }
});
</script>
<style>
  .slider-container {
    width: 80%;
    margin: 50px auto;
  }

  .slider {
    -webkit-appearance: none;
    width: 100%;
    height: 15px;
    border-radius: 5px;
    background: #d3d3d3;
    outline: none;
    opacity: 0.7;
    transition: opacity .2s;
  }

  .slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    background: #4CAF50;
    cursor: pointer;
  }

  .slider::-moz-range-thumb {
    width: 25px;
    height: 25px;
    border-radius: 50%;
    background: #4CAF50;
    cursor: pointer;
  }
</style>