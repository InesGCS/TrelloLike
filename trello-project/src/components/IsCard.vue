<template>
<div id="isCard">
<!-- <p class="cardTitle">
    {{ card.title.rendered }}
</p> -->
    <!-- <p v-html="card.content.rendered"></p> -->
    <!-- {{ card.content.rendered }} -->
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
