<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 class="iframe-wrapper">
      <template v-for="(comment, i) in comments">
        <comment :key="i" :comment="comment" :my-id="id"></comment>
      </template>
      <iframe
        class="iframe"
        :src="url"
        frameborder="0"
        width="960"
        height="569"
        allowfullscreen="true"
        mozallowfullscreen="true"
        webkitallowfullscreen="true"
      />
    </v-flex>
    <v-bottom-navigation v-model="bottomNav" dark height="32" class="bottomNav">
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
    <v-bottom-navigation v-model="bottomNav" dark height="70" class="bottomNav">
      <v-col cols="11">
        <v-text-field
          v-model="freeComment"
          label="LTで質問があれば入力してね！"
        />
      </v-col>
      <v-col cols="1" class="d-flex justify-center">
        <v-btn @click="sendFreeMessage">
          <v-icon>mdi-send</v-icon>
        </v-btn>
      </v-col>
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
      socket: '',
      freeComment: '',
      url: ''
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

    this.url = this.$route.query.src
  },
  methods: {
    click(e) {
      this.socket.emit('ADD_COMMENT', {
        id: this.id,
        comment: e.target.textContent,
        top: Math.floor(Math.random() * 90)
      })
    },
    sendFreeMessage() {
      if (!this.freeComment || !this.freeComment.trim()) return
      this.socket.emit('ADD_COMMENT', {
        id: this.id,
        comment: this.freeComment,
        top: Math.floor(Math.random() * 90)
      })
      this.freeComment = ''
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
  height: calc(100vh - 160px);
}

.bottomNav {
  overflow-x: scroll;
  justify-content: space-between;
}
</style>
