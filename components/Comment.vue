<template>
  <div :class="getClass" :style="{ top: `${comment.top}%` }">
    <span>{{ comment.comment }}</span>
  </div>
</template>

<script>
export default {
  props: {
    comment: { type: Object, required: true },
    myId: { type: String, required: true }
  },
  computed: {
    getClass() {
      if (this.comment.id === this.myId) {
        return {
          comment: true,
          'comment-id-match': true
        }
      }
      return {
        comment: true
      }
    }
  }
}
</script>

<style scoped>
.comment-id-match {
  border: solid 1px yellow;
}

.comment {
  position: absolute;
  top: 0%;
  animation-name: comment-scroll;
  animation-duration: 5s;
  animation-timing-function: linear;
  font-size: 40px;
  opacity: 0;
  word-break: keep-all;
  pointer-events: none;
}

@keyframes comment-scroll {
  0% {
    right: 0;
    opacity: 1;
  }
  100% {
    right: 100%;
    opacity: 1;
  }
}
</style>
