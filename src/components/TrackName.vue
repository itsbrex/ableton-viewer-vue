<template>
  <div :style="{ height: layoutSettings.trackHeight + 'px', paddingBottom: layoutSettings.trackPB + 'px' }"
        class="pl-0">
    <div :style="{ backgroundColor: bgColour }" class="h-100 p-2">
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
      bgColour: Colours.getColourByIndex(this.colourIndex),
    }
  }
};
</script>