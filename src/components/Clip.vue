<template>
  <div class="position-absolute bg-warning border-right text-truncate" :style="mainStyle">
    <small :style="smallStyle">{{ name }}</small>
  </div>
</template>

<script>
export default {
  props: {
    name: String,
    start: Number,
    length: Number,
    magnifyFactor: Number,
  },
  computed: {
    mainStyle() {
      // console.log(this.length, this.start);
      return  {
                left: (this.start * this.magnifyFactor) + "px",
                width: (this.length * this.magnifyFactor) + "px",
                height: "100%",
              };
    },
    smallStyle() {
      return  {
                fontSize: "6pt",
                padding: "2px",
              };
    },
  }
}
</script>

<style>

</style>