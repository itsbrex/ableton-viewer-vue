<template>
  <div class="w-100">

    <!-- Header -->
    <header class="row d-flex align-items-center justify-content-center mb-4 mt-1">
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
         class="row input-group mb-4">
      <div class="input-group-prepend">
        <span class="input-group-text">File:</span>
      </div>
      <div class="custom-file">
        <input
           @change="processFile"
           type="file"
           class="custom-file-input"
           id="inputGroupFile01"
           aria-describedby="chooseFile">
        <label class="custom-file-label" for="chooseFile">Drop an .als file here, or click to browse</label>
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
    <TracksView v-if="file.isValid"
            :project="relevantProperties" />

    <!-- JSON representation of project -->
    <div v-if="file.isValid"
         class="border-top bg-white rounded p-4 w-100 mt-4 shadow d-flex justify-content-center">
      <button v-if="!file.showJSON"
              @click="file.showJSON = true"
              class="btn btn-warning">Show JSON (this could be very big)</button>
      <h2 v-if="file.showJSON">Project (JSON)</h2>
      <JSON v-if="file.showJSON"
            :abletonProject="new Object(abletonProject)" />
    </div>
  </div>
</template>

<script>
import Zlib from 'zlib';
import xml2js from 'xml2js';
import Vue from "vue";
import JSON from "./JSON.vue";
import TracksView from "./TracksView.vue";

export default {
  name: 'Main',
  components: {
    JSON,
    TracksView,
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
      abletonProject: null,
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
        metaData: {},
        tracks: {
        },
        length: 0,
      };

      project.creator = this.abletonProject.Creator;
      project.metaData = this.abletonProject.MetaData;
      
      project.tracks = [];
      this.abletonProject.LiveSet.Tracks.AudioTrack.forEach(track => {
        const clips = track.DeviceChain.MainSequencer.Sample.ArrangerAutomation.Events.AudioClip;
        // console.log("Audio Clips: ", clips);
        let newClips = [];

        if (Array.isArray(clips)) {
          clips.forEach(clip => {
            newClips.push({ time: parseFloat(clip.Time),
                            name: clip.Name.Value,
                            currentStart: parseFloat(clip.CurrentStart.Value),
                            currentEnd: parseFloat(clip.CurrentEnd.Value),
                            colourIndex: parseFloat(clip.ColorIndex.Value),
            });
            if (parseFloat(clip.CurrentEnd.Value) > project.length) project.length = parseFloat(clip.CurrentEnd.Value);
          });
        } else if (typeof clips === "object") {
          newClips.push({
                            time: parseFloat(clips.Time),
                            name: clips.Name.Value,
                            currentStart: parseFloat(clips.CurrentStart.Value),
                            currentEnd: parseFloat(clips.CurrentEnd.Value),
                            colourIndex: parseFloat(clips.ColorIndex.Value),
          });
        }

        project.tracks.push({ id: track.Id,
                              name: track.Name.EffectiveName.Value,
                              type: "Audio",
                              clips: newClips,
                              colourIndex: parseFloat(track.ColorIndex.Value),
        });
      });
      this.abletonProject.LiveSet.Tracks.MidiTrack.forEach(track => {
        const clips = track.DeviceChain.MainSequencer.ClipTimeable.ArrangerAutomation.Events.MidiClip;
        // console.log(clips);
        let newClips = [];

        if (Array.isArray(clips)) {
          clips.forEach(clip => {
            newClips.push({ time: parseFloat(clip.Time),
                            name: clip.Name.Value,
                            currentStart: parseFloat(clip.CurrentStart.Value),
                            currentEnd: parseFloat(clip.CurrentEnd.Value),
                            colourIndex: parseFloat(clip.ColorIndex.Value),
            });
            if (parseFloat(clip.CurrentEnd.Value) > project.length) project.length = parseFloat(clip.CurrentEnd.Value);
          });
        } else if (typeof clips === "object") {
          newClips.push({
                            time: parseFloat(clips.Time),
                            name: clips.Name.Value,
                            currentStart: parseFloat(clips.CurrentStart.Value),
                            currentEnd: parseFloat(clips.CurrentEnd.Value),
                            colourIndex: parseFloat(clips.ColorIndex.Value),
          });
        }

        project.tracks.push({ id: track.Id,
                              name: track.Name.EffectiveName.Value,
                              type: "Midi",
                              clips: newClips,
                              colourIndex: parseFloat(track.ColorIndex.Value),
        });
      });
      // this.abletonProject.LiveSet.Tracks.GroupTrack.forEach((track) => {
      //   project.tracks.push({ id: track.Id,
      //                         name: track.Name.EffectiveName.Value,
      //                         type: "Group",
      //   });
      // });
      // this.abletonProject.LiveSet.Tracks.ReturnTrack.forEach((track) => {
      //   project.tracks.push({ id: track.Id,
      //                         name: track.Name.EffectiveName.Value,
      //                         type: "Return",
      //   });
      // });


      // project.tracks.sort((a, b) => {
      //   return a.id - b.id;
      // })
        
      console.log("Relevant properties: ", project);

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
        parser.parseStringPromise(xml)
        .then((result) => {
          this.abletonProject = result.Ableton;
          this.abletonProject.MetaData = file;

          // Make sure that all tracks are arrays
          let liveSet = this.abletonProject.LiveSet;
          let tracks = liveSet.Tracks;
          if (typeof tracks.AudioTrack  === 'undefined') tracks.AudioTrack  = [];
          if (typeof tracks.MidiTrack   === 'undefined') tracks.MidiTrack   = [];
          if (typeof tracks.ReturnTrack === 'undefined') tracks.ReturnTrack = [];
          if (typeof tracks.GroupTrack  === 'undefined') tracks.GroupTrack = [];
          if (!Array.isArray(tracks.AudioTrack))  tracks.AudioTrack  = [tracks.AudioTrack];
          if (!Array.isArray(tracks.MidiTrack))   tracks.MidiTrack   = [tracks.MidiTrack];
          if (!Array.isArray(tracks.ReturnTrack)) tracks.ReturnTrack = [tracks.ReturnTrack];
          if (!Array.isArray(tracks.GroupTrack))  tracks.GroupTrack  = [tracks.GroupTrack];
          this.abletonProject.LiveSet.Tracks = tracks;

          Vue.set(this.file, "isValid", true);
          Vue.set(this.file, "isBeingProcessed", false);

          console.log("Ableton file:", this.abletonProject);
        });
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
