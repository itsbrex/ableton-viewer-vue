<template>
  <div class="w-100 bg-white shadow mb-2 p-2">
    <div class="d-flex justify-content-around mb-2">
      <span><strong>File name:</strong> {{ project.metaData.name }}</span>
      <span><strong>Last Modified:</strong> {{ lastModified }}</span>
      <span><strong>Created in:</strong> {{ project.creator }}</span>
    </div>

    <div class="d-flex pr-0 align-items-start border">
      <!-- Sequencer view -->
      <div class="col d-flex flex-column justify-content-center pl-0 pr-0 overflow-auto w-100">
        <table class="border-left border-bottom w-100">
          <thead class="xx-thead-bg">
            <tr>
              <th scope="col" class="p-2">Arrangement</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="track in project.tracks" :key="track.id" class="xx-tr-bg">
              <td>
                <TrackArrangement :clips="track.clips"
                                  :type="track.type"
                                  :layoutSettings="layoutSettings"/>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <!-- Track Names -->
      <div class="col-2 d-flex flex-column justify-content-center pr-0 pl-0 w-100">
        <table class="border-left border-right border-bottom w-100">
          <thead class="xx-thead-bg">
            <tr>
              <th scope="col" class="p-2">Track name</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="track in project.tracks" :key="track.id" class="xx-tr-bg">
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
        // In bound styles format
        styles: {
          trackHeight: { height: "40px" },
          trackPB: { paddingBottom: "2px" },
        },
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
.xx-thead-bg {
  background-color: gray;
}
.xx-tr-bg {
  background-color: lightblue;
}
</style>