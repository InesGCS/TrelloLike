<template>
<div id="isCard">
    <p @click="() => TogglePopUp('buttonTrigger')" v-html="card.content.rendered"></p>
    <PopUp
    v-if="popupTrigger.buttonTrigger"
    :TogglePopUp="() => TogglePopUp('buttonTrigger')"
    :card="card">
      <h1 v-html="card.content.rendered"></h1>
    </PopUp>

</div>
</template>

<script>
import { ref } from 'vue'
import PopUp from '../components/PopUp.vue'
export default {
  props: ['card'],
  components: {
    PopUp
  },
  setup () {
    const popupTrigger = ref({
      buttonTrigger: false
    })
    const TogglePopUp = (trigger) => {
      popupTrigger.value[trigger] = !popupTrigger.value[trigger]
    }
    return {
      popupTrigger,
      TogglePopUp
    }
  },
  data () {
    return {
    }
  }
}

</script>

<style scoped>
#isCard {
  background-color: lemonchiffon;
  cursor: pointer;
}

p:hover {
  background-color: #e1e1ea;
}
</style>
