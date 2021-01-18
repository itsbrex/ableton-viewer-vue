<template>
  <div class="position-absolute border-right text-truncate xx-hover" :style="mainStyle">
    <small :style="smallStyle">{{ name }}</small>
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
  computed: {
    colour () {
      console.log(Colours.getColourByIndex(this.colourIndex));
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
  }
</style>