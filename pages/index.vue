<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 class="iframe-wrapper">
      <template v-for="(comment, i) in comments">
        <comment :key="i" :comment="comment" :my-id="id"></comment>
      </template>
      <iframe
        class="iframe"
        src="https://docs.google.com/presentation/d/e/2PACX-1vTTxPk_lxaLI7sBxHP6h1KasvBR5ecJZDE0jE_lz5sojF9aR0SrZnsB07RsAtwqqwPCxKA-HEHfa6Nb/embed?start=false&loop=false&delayms=3000"
        frameborder="0"
        width="960"
        height="569"
        allowfullscreen="true"
        mozallowfullscreen="true"
        webkitallowfullscreen="true"
      />
    </v-flex>
    <v-bottom-navigation v-model="bottomNav" dark class="bottomNav">
      <v-btn @click="click">
        <span>💩</span>
      </v-btn>
      <v-btn @click="click">
        <span>✨</span>
      </v-btn>
      <v-btn @click="click">
        <span>💖</span>
      </v-btn>
      <v-btn @click="click">
        <span>🤪</span>
      </v-btn>
      <v-btn @click="click">
        <span>ｲｲﾈ!!</span>
      </v-btn>
      <v-btn @click="click">
        <span>社外から</span>
      </v-btn>
      <v-btn @click="click">
        <span>家から</span>
      </v-btn>
    </v-bottom-navigation>
  </v-layout>
</template>

<script>
import io from 'socket.io-client'
import Comment from '~/components/Comment'

export default {
  components: {
    Comment
  },
  data: () => {
    return {
      id: '',
      comments: [],
      bottomNav: 0,
      socket: ''
    }
  },
  computed: {
    bottomNavComp() {
      return this.bottomNav
    }
  },
  mounted() {
    this.socket = io('https://ppc-server.unubo.app')

    this.socket.on('connect', () => {
      this.id = this.socket.id
    })

    this.socket.on('ADD_COMMENT', (comment) => {
      this.comments.push(comment)
    })
  },
  methods: {
    click(e) {
      this.socket.emit('ADD_COMMENT', {
        id: this.id,
        comment: e.target.textContent,
        top: Math.floor(Math.random() * 90)
      })
    }
  }
}
</script>

<style scoped>
.iframe-wrapper {
  width: 100%;
  position: relative;
}

.iframe {
  width: 100%;
  height: calc(100vh - 80px);
}

.bottomNav {
  overflow-x: scroll;
  justify-content: normal;
}
</style>
