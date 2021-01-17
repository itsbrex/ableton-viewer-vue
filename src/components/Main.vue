<template>
  <div class="w-100">

    <!-- Header -->
    <header class="row d-flex align-items-center mb-4 mt-1">
      <h1 class="">Ableton Viewer (Vue)</h1>
      <!-- Choose a different file button -->
      <div v-if="file.isValid"
           class="ml-2">
        <button @click="file.isValid = false"
                class="btn btn-primary btn-sm">reset</button>
      </div>
    </header>

    <!-- File input dialogue -->
    <div v-if="!file.isBeingProcessed && !file.isValid"
         class="row input-group mb-3">
      <div class="input-group-prepend">
        <span class="input-group-text">Select an .als file</span>
      </div>
      <div class="custom-file">
        <input
           @change="processFile"
           type="file"
           class="custom-file-input"
           id="inputGroupFile01"
           aria-describedby="chooseFile">
        <label class="custom-file-label" for="chooseFile">Choose file</label>
      </div>
    </div>

    <!-- Loading indicator -->
    <div v-if="file.isBeingProcessed"
         class="row align-items-center flex-column">
      <div class="spinner-border text-primary mb-4" role="status">
        <span class="sr-only">Loading...</span>
      </div>
      <p>(You might have to be patient)</p>
    </div>

    <!-- Error message -->
    <div v-if="file.gaveError"
         class="row alert alert-danger" role="alert">
      There was an issue processing your file. Please ensure it was a valid .als file.
    </div>

    <!-- Visual representation -->
    <Tracks v-if="file.isValid"
            :project="relevantProperties" />

    <!-- JSON representation of project -->
    <div v-if="file.isValid"
         class="border-top bg-light rounded p-4 w-100 shadow d-flex justify-content-center">
      <button v-if="!file.showJSON"
              @click="file.showJSON = true"
              class="btn btn-warning">Show JSON (this could be very big)</button>
      <h2 v-if="file.showJSON">Project (JSON)</h2>
      <JSON v-if="file.showJSON"
            :projectObject="projectObject" />
    </div>
  </div>
</template>

<script>
import Zlib from 'zlib';
import xml2js from 'xml2js';
import Vue from "vue";
import JSON from "./JSON.vue";
import Tracks from "./Tracks.vue";

export default {
  name: 'Main',
  components: {
    JSON,
    Tracks,
  },
  props: {
    msg: String
  },
  data() {
    return {
      dropzoneOptions: {
        acceptedFiles: ".als",
        url: "#",
      },
      projectObject: null,
      file: {
        isBeingProcessed: false,
        isValid: false,
        gaveError: false,
        showJSON: false,
      },
    }
  },
  computed: {
    relevantProperties() {
      let project = {
        creator: "",
        tracks: {
          audioTracks: {},
        }
      };

      project.creator = this.projectObject.Creator;
      
      project.tracks = [];
      this.projectObject.LiveSet.Tracks.AudioTrack.forEach((track) => {
        project.tracks.push({ id: track.Id,
                              name: track.Name.EffectiveName.Value,
                              type: "Audio",
        });
      });
      this.projectObject.LiveSet.Tracks.MidiTrack.forEach((track) => {
        project.tracks.push({ id: track.Id,
                              name: track.Name.EffectiveName.Value,
                              type: "Midi",
        });
      });
      // this.projectObject.LiveSet.Tracks.GroupTrack.forEach((track) => {
      //   project.tracks.push({ id: track.Id,
      //                         name: track.Name.EffectiveName.Value,
      //                         type: "Group",
      //   });
      // });
      // this.projectObject.LiveSet.Tracks.ReturnTrack.forEach((track) => {
      //   project.tracks.push({ id: track.Id,
      //                         name: track.Name.EffectiveName.Value,
      //                         type: "Return",
      //   });
      // });


      // project.tracks.sort((a, b) => {
      //   return a.id - b.id;
      // })
        
      console.log(project);

      return project;
    },
  },
  methods: {
    async processFile(event) {
      Vue.set(this.file, "isBeingProcessed", true);
      Vue.set(this.file, "gaveError", false);

      const file = event.target.files[0];
      const arrayBuffer = await file.arrayBuffer();

      try {
        let fileBuffer = Buffer.from(arrayBuffer);

        // Unzip the file
        let xml = Zlib.gunzipSync(fileBuffer).toString();

        // Convert the XML to a javascript object
        let parser = new xml2js.Parser({mergeAttrs: true, explicitArray: false});
        parser.parseString(xml,
          (err, result) => {
            if(err){
                return;
            }
            this.projectObject = result.Ableton;
            console.log("HERE:", this.projectObject);
          });

          // Make sure that all tracks are arrays
          let liveSet = this.projectObject.LiveSet;
          let tracks = liveSet.Tracks;
          if (typeof tracks.AudioTrack  === 'undefined') tracks.AudioTrack  = [];
          if (typeof tracks.MidiTrack   === 'undefined') tracks.MidiTrack   = [];
          if (typeof tracks.ReturnTrack === 'undefined') tracks.ReturnTrack = [];
          if (typeof tracks.GroupTrack  === 'undefined') tracks.GroupTrack = [];
          if (!Array.isArray(tracks.AudioTrack))  tracks.AudioTrack  = [tracks.AudioTrack];
          if (!Array.isArray(tracks.MidiTrack))   tracks.MidiTrack   = [tracks.MidiTrack];
          if (!Array.isArray(tracks.ReturnTrack)) tracks.ReturnTrack = [tracks.ReturnTrack];
          if (!Array.isArray(tracks.GroupTrack))  tracks.GroupTrack  = [tracks.GroupTrack];
          this.projectObject.LiveSet.Tracks = tracks;

          Vue.set(this.file, "isValid", true);
          Vue.set(this.file, "isBeingProcessed", false);

          console.log("Ableton file:", this.projectObject);
      } catch (error) {
        Vue.set(this.file, "isValid", false);
        Vue.set(this.file, "isBeingProcessed", false);
        Vue.set(this.file, "gaveError", true);
      }
    }
  },
}
</script>

<style scoped>
</style>
