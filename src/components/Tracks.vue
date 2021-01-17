<template>
  <div class="w-100 bg-light shadow mb-2 p-2">
    <h2>{{ project.creator }}</h2>
    <div class="w-100 row">
      <!-- Sequencer view -->
      <div class="col d-flex flex-column">

      </div>
      <!-- Track Names -->
      <table class="col-2 table table-striped">
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
</template>

<script>
import "@fortawesome/fontawesome-free/css/all.css"

export default {
  name: "Tracks",
  props: {
    project: Object,
  },
};
</script>
