<template>
<div id="isComment">
    <p v-html="comment.content.rendered"></p>
    <button id='deleteCommentButton' @click='removeComment(comment.id)'>X</button>
</div>
</template>

<script>
export default {
  props: ['comment'],

  data () {
    return {
    }
  }
}

</script>
