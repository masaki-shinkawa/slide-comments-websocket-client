<template>
  <v-app dark>
    <v-content>
      <v-container fill-height>
        <v-layout wrap>
          <v-flex xs12 class="iframe-wrapper">
            <v-col cols="12">
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
              <article v-show="isShowComment" class="comment-area">
                <ul>
                  <template v-for="(comment, i) in lastComment">
                    <li :key="i">{{ comment.comment }}</li>
                  </template>
                </ul>
              </article>
            </v-col>
          </v-flex>
          <v-flex xs12>
            <v-bottom-navigation
              v-model="bottomNav"
              dark
              height="32"
              class="bottomNav"
            >
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
                <span>🌝</span>
              </v-btn>
              <v-btn @click="click">
                <span>🤔</span>
              </v-btn>
              <v-btn @click="switchShowComment">
                <v-icon>mdi-comment</v-icon>
              </v-btn>
            </v-bottom-navigation>
          </v-flex>
          <v-flex xs12>
            <v-bottom-navigation v-model="bottomNav" dark class="bottomNav">
              <v-col cols="11">
                <input
                  v-model="freeComment"
                  class="comment-input"
                  placeholder="LTで質問があれば入力してね！"
                />
              </v-col>
              <v-btn text icon x-small min-width="40" @click="sendFreeMessage">
                <v-icon>mdi-send</v-icon>
              </v-btn>
            </v-bottom-navigation>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
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
      url: '',
      isShowComment: false
    }
  },
  computed: {
    bottomNavComp() {
      return this.bottomNav
    },
    lastComment() {
      return this.comments.slice(-5)
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
    switchShowComment() {
      this.isShowComment = !this.isShowComment
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
  height: calc(100vh - 140px);
}

.comment-area {
  position: absolute;
  bottom: 35px;
}

.comment-input {
  width: 100%;
}

.bottomNav {
  overflow-x: scroll;
  justify-content: space-between;
}
div::-webkit-scrollbar {
  /* Chrome, Safari 対応 */
  display: none;
}
</style>
