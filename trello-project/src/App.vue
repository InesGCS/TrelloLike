<template>
  <div class="page">

<ListManager>This is the first view</ListManager>
</div>
</template>

<script>
import ListManager from './components/ListManager.vue'

export default {
  components: {
    ListManager
  }
}
</script>

<style scoped>
.page {
    background-color: antiquewhite;
    size: 100%;
    height: 100%;
}
</style>
