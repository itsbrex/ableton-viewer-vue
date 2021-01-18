<template>
  <div class="w-100 bg-white shadow mb-2 p-2">
    <div class="d-flex justify-content-around mb-2">
      <span><strong>File name:</strong> {{ project.metaData.name }}</span>
      <span><strong>Last Modified:</strong> {{ lastModified }}</span>
      <span><strong>Created in:</strong> {{ project.creator }}</span>
    </div>

    <div class="d-flex pr-0 align-items-start">
      <!-- Sequencer view -->
      <div class="col d-flex flex-column justify-content-center pl-0 pr-0 w-100 xx-arrangement-bg"
           style="border-right: 1px solid black">
        <h4 class="p-1 m-0 xx-arrangement-header-bg text-white"
            style="border-bottom: 1px solid black"
            :style="{ height: layoutSettings.trackHeight + 'px' }">Arrangement</h4>
        <div class="overflow-auto border-right">
          <table class="border-bottom w-100 pl-0">
            <!-- The horizontal dividing line between tracks -->
            <tbody class="position-absolute w-100"
                   style="z-index: 1;">
              <tr v-for="track in project.tracks" :key="track.id"
                  class="position-relative d-inline-block xx-lines-bg w-100"
                  :style="{ height: layoutSettings.trackHeight + 2 + 'px', paddingBottom: layoutSettings.trackPB + 'px' }">
                <td class="w-100">
                </td>
              </tr>
            </tbody>
            <!-- The tracks -->
            <tbody class="position-relative" style="z-index: 2">
              <tr v-for="track in project.tracks" :key="track.id" class="">
                <td class="">
                  <TrackArrangement :clips="track.clips"
                                    :type="track.type"
                                    :layoutSettings="layoutSettings"/>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <!-- Track Names -->
      <div class="col-2 d-flex flex-column justify-content-center pr-0 pl-0 w-100 xx-names-bg">
        <h4 class="p-1 m-0 xx-names-header-bg text-white"
            style="border-bottom: 1px solid black"
            :style="{ height: layoutSettings.trackHeight + 'px' }"
            >Track name</h4>
        <table class="border-left w-100">
          <tbody>
            <tr v-for="track in project.tracks" :key="track.id" class="">
              <td>
                <TrackName :name="track.name"
                           :type="track.type"
                           :layoutSettings="layoutSettings"
                           :colourIndex="track.colourIndex"/>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import TrackName from "./TrackName.vue";
import TrackArrangement from "./TrackArrangement.vue";
import "@fortawesome/fontawesome-free/css/all.css";

export default {
  name: "TracksView",
  components: {
    TrackName,
    TrackArrangement,
  },
  props: {
    project: Object,
  },
  data() {
    return {
      layoutSettings: {
        trackHeight: 40,
        trackPB: 1,
      },
    }
  },
  computed: {
    lastModified() {
      let timestamp = this.project.metaData.lastModified;
      console.log(timestamp);
      const date = new Date(timestamp);

      return date.toDateString() + " (" + date.toTimeString().substring(0, date.toTimeString().indexOf(" ")) + ")";
    }
  }
};
</script>
<style scoped>
.xx-lines-bg {
  z-index: 1;
  pointer-events: none;
  overflow: hidden;
  border-bottom: 1px black dotted;
}
.xx-names-header-bg {
  background-color: rgba(0, 0, 0, 0.6);
}
.xx-names-bg {
  background-color: rgba(0, 0, 0, 0.3);
}
.xx-arrangement-header-bg {
  background-color: rgba(0, 0, 0, 0.5);
}
.xx-arrangement-bg {
  background-color: rgba(0, 0, 0, 0.2);
}
</style>