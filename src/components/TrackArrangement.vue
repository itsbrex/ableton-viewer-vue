<template>
  <span>
    <span v-if="clips.length !== 0 && (type === 'Audio' || type === 'Midi')"
          class="text-nowrap position-relative">
      <!-- Go through all the clips -->
      <Clip v-for="clip in clips" :key="clip.id"
            :start="clip.currentStart"
            :length="clip.currentEnd - clip.currentStart"
            :magnifyFactor="magnifyFactor"
            :name="clip.name"
            :colourIndex="clip.colourIndex" />
      _
    </span>
    <!-- If there are no clips -->
      <span v-if="clips.length === 0">
        (empty)
      </span>
  </span>
</template>

<script>
import Clip from "./Clip.vue";

export default {
  components: {
    Clip,
  },
  props: {
    clips: Array,
    type: String,
    layout: Object,
  },
  data() {
    return {
      magnifyFactor: 4,
    }
  }
}
</script>

<style>

</style>