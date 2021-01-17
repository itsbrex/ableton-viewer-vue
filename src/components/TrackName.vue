<template>
  <div>
    <span>
      {{ name }}
    </span>
    <span class="float-right">
      <span v-if="type === 'Midi'" class="fas fa-music"> </span>
      <span v-if="type === 'Audio'" class="fas fa-microphone-alt"> </span>
    </span>
  </div>
</template>

<script>
export default {
  props: {
    name: String,
    type: String,
  }
};
</script>