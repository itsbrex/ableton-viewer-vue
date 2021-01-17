<template>
  <div :style="bgColour">
    <span>
      {{ name }}
    </span>
    <span class="float-right">
      <span v-if="type === 'Midi'" class="fas fa-music"> </span>
      <span v-if="type === 'Audio'" class="fas fa-microphone-alt"> </span>
    </span>
  </div>
</template>

<script>
import Colours from "./Colours.js";

export default {
  props: {
    name: String,
    type: String,
    colourIndex: Number,
  },
  data() {
    return {
      bgColour: { backgroundColor: Colours.getColourByIndex(this.colourIndex) },
    }
  }
};
</script>