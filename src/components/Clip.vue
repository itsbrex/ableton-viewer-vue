<template>
  <div  class="position-absolute border-right xx-hover"
        :style="mainStyle"
        style="overflow: visible"
        @mouseover="hover = true"
        @mouseleave="hover = false">
    <small class="w-100 text-truncate" :style="smallStyle">{{ name }}</small>
    <div v-if="hover === true"
         class="position-absolute bg-white"
         style="pointer-events: none;">
      {{ name }}
    </div>
  </div>
</template>

<script>
import Colours from "./Colours.js";

export default {
  props: {
    name: String,
    start: Number,
    length: Number,
    magnifyFactor: Number,
    colourIndex: Number,
  },
  data() {
    return {
      hover: false,
    }
  },
  computed: {
    colour () {
      // console.log(Colours.getColourByIndex(this.colourIndex));
      return Colours.getColourByIndex(this.colourIndex);
    },
    mainStyle() {
      // console.log(this.length, this.start);
      return  {
                display: "flex",
                alignItems: "flex-end",
                left: (this.start * this.magnifyFactor) + "px",
                width: (this.length * this.magnifyFactor) + "px",
                height: "100%",
                backgroundColor: this.colour,
              };
    },
    smallStyle() {
      return  {
                fontSize: "6pt",
                padding: "2px",
                cursor: "default",
              };
    },
  }
}
</script>

<style>
  .xx-hover:hover {
    opacity: 0.7;
    z-index: 1000;
  }
</style>