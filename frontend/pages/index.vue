<template>
  <div :class="{ container: !breakpoint.xsOnly }">
    <div
      :class="{
        'side-area-sp': breakpoint.xsOnly,
        'side-area-pc': !breakpoint.xsOnly
      }"
    >
      <Sidebar />
    </div>
    <div class="post-area">
      <Content v-for="post in posts" :key="post.id" :post="post" />
      <Form />
    </div>
  </div>
</template>

<script>
import Sidebar from '~/components/Sidebar.vue'
import Form from '~/components/Form.vue'
import Content from '~/components/Content.vue'

export default {
  components: { Sidebar, Content, Form },
  data() {
    return {
      isHydrated: false
    }
  },
  computed: {
    posts() {
      return this.$store.getters.posts
    },
    breakpoint() {
      return this.isHydrated ? this.$vuetify.breakpoint : {}
    }
  },
  mounted() {
    this.$store.dispatch('nuxtClientInit')
    this.isHydrated = true
    const $ = el => document.querySelector(el)
    $('html').scrollTo({ top: 9999999 })
    this.$firestore.collection('posts').onSnapshot(snapshot => {
      if (snapshot.docChanges().length !== 1) return
      snapshot.docChanges().forEach(change => {
        const post = change.doc.data()
        this.$store.commit('addPost', { post })
      })
    })
  }
}
</script>

<style scoped>
.container {
  display: flex;
  align-items: flex-start;
}

.side-area-sp {
  width: 100%;
  position: sticky;
  top: 0;
  background-color: #303030;
  opacity: 0.9;
}

.side-area-pc {
  width: 300px;
  position: sticky;
  top: 0;
}

.post-area {
  flex: 1;
  display: flex;
  flex-direction: column;
  border-left: solid 1px rgba(255, 255, 255, 0.1);
  padding: 16px 16px 0;
}
</style>
