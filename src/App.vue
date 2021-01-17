<template>
  <div id="app" class="container-fluid d-flex flex-column align-items-center m-1">
    <Main />
  </div>
</template>

<script>
import Main from './components/Main.vue';
import 'bootstrap/dist/css/bootstrap.min.css';

export default {
  name: 'App',
  components: {
    Main
  }
}
</script>

<style>
</style>
