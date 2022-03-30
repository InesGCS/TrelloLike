<template>
<div id="isCard">
    <p v-html="card.content.rendered"></p>
</div>
</template>

<script>
export default {
  props: ['card']
}

</script>

<style scoped>
#isCard {
  background-color: lemonchiffon;
}
</style>
