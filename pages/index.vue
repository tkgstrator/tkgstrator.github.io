<template>
  <div id="app">
    <header class="navigation-bar">
      <h1>Salmon Project</h1>
    </header>
    <div class="page coop-content">
      <div class="coop-content-header">
        <h2><span>Salmon Run</span></h2>
        <h3><span>iPhone 12 Tournament</span></h3>
        <span @click="signIn" class="coop-join" v-if="this.loginUser == null"
          >参加する</span
        >
      </div>
      <div class="coop-content-list-wrapper">
        <div id="coop-content-list">
          <div v-html="Overview" class="project-content-overview"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import marked from "marked";
import Overview from "~/pages/md/index.md"

export default {
  computed: {
    source() {
      return Overview;
    },
    Overview() {
      return marked(this.source);
    },
  },
  async created() {
    this.$fireAuth.onAuthStateChanged(async user => {
      await this.readFromFirestore()
      this.loginUser = this.$fireAuth.currentUser
      if (this.loginUser) {
        const result = this.user.filter(user => user.uid == this.loginUser.uid)
        console.log(result, this.user)
        if (result.length == 0) {
          this.createUser()
        }
      }
    })
  },
  methods: {
    async readFromFirestore() {
      const userRef = this.$fireStore.collection("users")
      try {
        const userRefs = await userRef.get().then(snapshot => {
          snapshot.forEach(document => {
            console.log(document.data())
            this.user.push(document.data())
          })
        })
      } catch (error) {
        alert(error)
        return
      }
    },
    async createUser() {
      const userRef = this.$fireStore.collection("users").doc(this.loginUser.uid)
      try {
        await userRef.set({
          golden_eggs: 0,
          power_eggs: 0,
          value: 5,
          nickname: this.loginUser.displayName,
          uid: this.loginUser.uid
        })
      } catch (error) {
        alert(error)
        return
      }

    },
    async signIn() {
      var provider = new this.$fireAuthObj.TwitterAuthProvider();
      this.$fireAuth.signInWithPopup(provider).then(function (result) {
        if (result.credential) {
          const token = result.credential.accessToken
          const secret = result.credential.secret
          const user = result.user
          console.log(user.uid)
        }
      }).catch(function (error) {
        const errorCode = error.code
        const errorMessage = error.message
        const email = error.email
        const credential = error.credential
      })
    }
  },
  data() {
    return {
      user: [],
      loginUser: null
    }
  }
}
</script>

<style lang="scss">
@import "~assets/sass/splatnet2.scss";
@import "~assets/sass/salmonproject.scss";
</style>