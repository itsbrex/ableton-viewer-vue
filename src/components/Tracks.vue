<template>
  <div class="w-100 bg-light shadow mb-2 p-2">
    <div class="d-flex justify-content-around mb-2">
      <span><strong>File name:</strong> {{ project.metaData.name }}</span>
      <span><strong>Last Modified:</strong> {{ lastModified }}</span>
      <span><strong>Created in:</strong> {{ project.creator }}</span>
    </div>

    <div class="w-100 row pr-0">
      <!-- Sequencer view -->
      <div class="col d-flex flex-column justify-content-center pr-0">
        <table class="table table-striped border-right border-left border-bottom">
          <thead>
            <tr>
              <th scope="col">Arrangement</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="track in project.tracks" :key="track.id">
              <td>
                {{ track.id }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <!-- Track Names -->
      <div class="col-2 d-flex flex-column justify-content-center pr-0 pl-0">
        <table class="table table-striped border-left border-right border-bottom">
          <thead>
            <tr>
              <th scope="col">Track name</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="track in project.tracks" :key="track.id">
              <td>
                <span>
                  {{ track.name }}
                </span>
                <span class="float-right">
                  <span v-if="track.type === 'Midi'"
                        class="fas fa-music">
                  </span>
                  <span v-if="track.type === 'Audio'"
                        class="fas fa-microphone-alt">
                  </span>
                </span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import "@fortawesome/fontawesome-free/css/all.css"

export default {
  name: "Tracks",
  props: {
    project: Object,
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
