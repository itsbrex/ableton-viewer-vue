<template>
  <div class="w-100 mw-100 overflow-auto">
    <json-viewer :value="abletonProject"/>
  </div>
</template>

<script>
import JsonViewer from 'vue-json-viewer';
import 'vue-json-viewer/style.css';

export default {
  components: {
    JsonViewer
  },
  props: {
    abletonProject: Object,
  },
  computed: {
    projectJSON() {
      return JSON.stringify(this.abletonProject);
    }
  }
};
</script>
