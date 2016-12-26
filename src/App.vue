<template>  
<div class="app">   
    <timer></timer>

</div>
  

</template>

<script>

import timer from './components/timer.vue'
export default {
  name: 'app',
  components: {
    timer
  }
}
  
</script>

 <style lang="sass">

html, body {
  height: 100%;
  margin: 0;
}

.app {
  min-height: 100%; 
}

</style>

