<template>
  <div id="app">
    <header class="navigation-bar">
      <h1>Salmon Project</h1>
    </header>
    <div class="page coop-content">
      <div class="coop-content-header">
        <h2><span>Salmon Run</span></h2>
        <h3>
          <span
            >所持金イクラ数: {{ user == null ? 0 : user.golden_eggs - user.used }} </span
          >
        </h3>
        <h3>
          <span v-if="user != null"
            >N: {{ user.result[0] }} R: {{ user.result[1]}} SR: {{ user.result[2] }} SSR: {{ user.result[3] }} UR: {{ user.result[4] }}</span
          >
        </h3>
      <div>
        <span @click="Single(1)" class="coop-join">
          単発(10)
        </span>
        <span @click="Single(11)" class="coop-join">
          11連(100)
        </span>
      </div>
      </div>
      <div class="coop-content-list-wrapper">
        <div id="coop-content-list">
          <table>
            <thead>
              <tr>
                <th>Title</th>
                <th>Price</th>
              </tr >
            </thead>
            <tbody>
              <tr v-for="(present, index) in presents">
                <td>{{ present.title }}</td>
                           <td>{{ present.price }}</td>
              </tr>
            </tbody>
          </table>
          <!-- <div v-html="Overview" class="project-content-overview"></div> -->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async created() {
    this.$fireAuth.onAuthStateChanged(async user => {
      // ログイン情報からFirestoreの情報を取得
      await this.readFromFirestore(user.uid)
      // console.log(this.loginUser.golde)
      // if (this.loginUser) {
      //   const result = this.user.filter(user => user.uid == this.loginUser.uid)
      //   console.log(result, this.user, this.loginUser)
      //   if (result.length == 0) {
      //     this.createUser()
      //   }
      // }
    })
  },
  mounted: function () {
    const userRef = this.$fireStore.collection("presents")
    try {
      const userRefs = userRef.get().then(snapshot => {
        snapshot.forEach(document => {
          this.presents.push(document.data())
        })
      })
    } catch (error) {
      alert(error)
      return
    }
    // Salmon Statsからデータを取得する
    // const url = "https://salmon-stats-api.yuki.games/api/players/91d160aa84e88da6/schedules/2021010106"
    // fetch(url).then(function(response) {
    //   console.log(response)
    // })
  },
  methods: {
    async Single(num) {
      const cost = num == 11 ? 100 : 10
      if (this.user.golden_eggs > cost) {
        try {
          let result = [0, 0, 0, 0, 0]
          for (let idx = 0; idx < num; ++idx) {
            const random = Math.floor(Math.random() * 1000)
            switch(true) { 
              case random == 0: // 0.1% 好きな作品ゲットチケット
                          result[4] += 1;
            break
          case random <= 10: // 1% 無料チケット
            result[3] += 1
            break
          case random <= 60: // 5% 75%オフチケット
            result[2] += 1
            break
          case random <= 160: // 10% 半額チケット
            result[1] += 1
            break
          default:
            result[0] += 1
        }
      }
      alert(`ガチャ結果: N:${result[0]} R: ${result[1]} SR:${result[2]} SSR:${result[3]} UR: ${result[4]}`)
      }
      const userRef = this.$fireStore.collection("users").doc(this.user.uid)
        await userRef.update({
          used: this.user.used + cost,
          result: result
        })
        this.user.used += cost
      } catch (error) {
        alert(error)
        return
      }
    },
    async Multiple() {

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
    },

        async readFromFirestore(uid) {
      const userRef = this.$fireStore.collection("users")
      try {
        const userRefs = await userRef.get().then(snapshot => {
          snapshot.forEach(document => {
            const user = document.data()
            if (uid == user.uid)
              this.user = user
            // console.log(document.data())
            // this.user.push(document.data())
          })
        })
      } catch (error) {
        alert(error)
        return
      }
    },
  },
  data() {
    return {
      user: null,
      golden_eggs: 0,
      presents: [],
    }
  },
}
</script>

<style lang="scss">
@import "~assets/sass/splatnet2.scss";
@import "~assets/sass/salmonproject.scss";
</style>