<template>
  <div :style="{ height: layoutSettings.trackHeight + 'px', paddingBottom: layoutSettings.trackPB + 'px' }">
    <div v-if="clips.length !== 0 && (type === 'Audio' || type === 'Midi')"
          class="text-nowrap position-relative h-100">
      <!-- Go through all the clips -->
      <Clip v-for="clip in clips" :key="clip.id"
            :start="clip.currentStart"
            :length="clip.currentEnd - clip.currentStart"
            :magnifyFactor="magnifyFactor"
            :name="clip.name"
            :colourIndex="clip.colourIndex" />
      
    </div>
    <!-- If there are no clips -->
      <span v-if="clips.length === 0" class="h-100 text-muted d-flex align-items-center pl-4">
        (no clips)
      </span>
  </div>
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
    layoutSettings: Object,
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