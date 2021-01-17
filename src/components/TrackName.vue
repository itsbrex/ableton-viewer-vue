<template>
  <div :style="[ layoutSettings.styles.trackHeight, layoutSettings.styles.trackPB, ]"
        class="">
    <div :style="[ bgColour ]" class="h-100 p-2">
      <span>
        {{ name }}
      </span>
      <span class="float-right">
        <span v-if="type === 'Midi'" class="fas fa-music"> </span>
        <span v-if="type === 'Audio'" class="fas fa-microphone-alt"> </span>
      </span>
    </div>
  </div>
</template>

<script>
import Colours from "./Colours.js";

export default {
  props: {
    name: String,
    type: String,
    colourIndex: Number,
    layoutSettings: Object,
  },
  data() {
    return {
      bgColour: { backgroundColor: Colours.getColourByIndex(this.colourIndex) },
    }
  }
};
</script>