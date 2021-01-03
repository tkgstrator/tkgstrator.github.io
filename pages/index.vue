<template>
  <div id="app">
    <header class="navigation-bar">
      <h1>Salmon Project</h1>
    </header>
    <div class="page coop-content">
      <div class="coop-content-header">
        <h2><span>Salmon Run</span></h2>
        <h3>
          <span>現在の推定参加者数: {{ random }} 人!!</span>
        </h3>
        <span @click="signIn" class="coop-join" v-if="this.loginUser == null">
          参加する
        </span>
        <nuxt-link
          to="/ranking"
          class="coop-join"
          v-if="this.loginUser != null"
        >
          ランキング確認
        </nuxt-link>
      </div>
      <div class="coop-content-list-wrapper">
        <div class="coop-schedule-list">
          <div class="coop-schedule-list-item">
            <h5>
              <time datetime="2020-10-22T08:00:00-04:00">10/23 9:00 p.m.</time>
              -
              <time datetime="2020-10-23T20:00:00-04:00">10/24 9:00 a.m.</time>
            </h5>
            <div class="coop-schedule-list-item-content">
              <div class="coop-schedule-list-stage">
                <img
                  class="coop-stage-image"
                  src="https://app.splatoon2.nintendo.net/images/coop_stage/50064ec6e97aac91e70df5fc2cfecf61ad8615fd.png"
                  alt=""
                />
                <p>Ruins of Ark Polaris</p>
              </div>
              <div class="coop-schedule-list-weapons">
                <p>Supplied Weapons</p>
                <ul>
                  <li>
                    <img
                      class="weapon-image"
                      src="https://app.splatoon2.nintendo.net/images/weapon/c60760efc43e3a509187a1b714be477f7fb6c514.png"
                      alt="Splat Dualies"
                    />
                  </li>
                  <li>
                    <img
                      class="weapon-image"
                      src="https://app.splatoon2.nintendo.net/images/weapon/193d0700fdbd08d58c72b7ac0e282984e77a7ffc.png"
                      alt="Carbon Roller"
                    />
                  </li>
                  <li>
                    <img
                      class="weapon-image"
                      src="https://app.splatoon2.nintendo.net/images/weapon/8f342949acc700b1603425071620dbae8536dbed.png"
                      alt="H-3 Nozzlenose"
                    />
                  </li>
                  <li>
                    <img
                      class="weapon-image"
                      src="https://app.splatoon2.nintendo.net/images/weapon/6abb5804369429bf96bb32c14d899c4b9da3e431.png"
                      alt="Goo Tuber"
                    />
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
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
    // this.initialize()
    this.$fireAuth.onAuthStateChanged(async user => {
      await this.readFromFirestore()
      this.loginUser = this.$fireAuth.currentUser
      if (this.loginUser) {
        const result = this.user.filter(user => user.uid == this.loginUser.uid)
        console.log(result, this.user, this.loginUser)
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
    async initialize() {
      this.presents.forEach(present => {
      const userRef = this.$fireStore.collection("presents").doc()
      try {
        userRef.set({
          title: present.title,
          price: present.price,
          maxprice: present.maxprice
        })
      } catch (error) {
        alert(error)
        return
      }
    })
    },
    async createUser() {
      const userRef = this.$fireStore.collection("users").doc(this.loginUser.uid)
      try {
        await userRef.set({
          uid: "", // SalmonStatsのID
          golden_eggs: 0, // 何回分リザルトを取得したかの関数
          used: 0,
          result: [0, 0, 0, 0, 0],
          nickname: this.loginUser.displayName,
          uid: this.loginUser.uid,
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
        console.log(result, this.user, this.loginUser)
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
            presents: [
        {
        title: "凪のあすから",
        price: 0,
        maxprice: 12000
        },
        {
         title: "蟲師",
         price: 0,
         maxprice: 6200
        },
        {
         title: "シュタインズ・ゲート",
         price: 0,
         maxprice: 5000,
        },
        {
         title: "緋弾のアリア",
         price: 0,
         maxprice: 6500
        },
        {
         title: "とある科学のレールガン",
         price: 0,
         maxprice: 5000,
        },
        {
         title: "とある科学のレールガンS",
         price: 0,
         maxprice: 5000,
        },
        {
         title: "城下町のダンデライオン",
         price: 0,
         maxprice: 3000,
        },
        {
         title: "神様のメモ帳",
         price: 0,
         maxprice: 6200,
        },
        {
         title: "境界の彼方",
         price: 0,
         maxprice: 7000,
        },
        {
         title: "言の葉の庭",
         price: 0,
         maxprice: 3000,
        },
        {
         title: "異能バトルは日常系のなかで",
         price: 0,
         maxprice: 7000,
        },
        {
         title: "ガーリッシュナンバー",
         price: 0,
         maxprice: 6000
        },
        {
         title: "たまこラブストーリー",
         price: 0,
         maxprice: 7000
        },
        {
         title: "きんいろモザイク",
         price: 0,
         maxprice: 6000
        },
        {
         title: "ハロー！きんいろモザイク",
         price: 0,
         maxprice: 6000
        },
        {
         title: "放課後のプレアデス",
         price: 0,
         maxprice: 6000
        },
        {
         title: "Wake Up Girls! 七人のアイドル",
         price: 0,
         maxprice: 3000
        },
        {
         title: "Wake Up Girls!",
         price: 0,
         maxprice: 6000
        },
        {
         title: "俺の妹がこんなに可愛いわけがない（一期）",
         price: 0,
         maxprice: 16000
        },
        {
         title: "俺の妹がこんなに可愛いわけがない（二期）",
         price: 0,
         maxprice: 16000
        },
        {
         title: "甘城ブリリアントパーク",
         price: 0,
         maxprice: 7000
        },
        {
         title: "氷菓（二枚組）",
         price: 0,
         maxprice: 7000
        },
        {
         title: "バトルプログラマーシラセ",
         price: 0,
         maxprice: 4000
        },
        {
         title: "True Tears",
         price: 0,
         maxprice: 4000
        },
        {
         title: "君の名は。",
         price: 0,
         maxprice: 3500
        },
        {
         title: "涼宮ハルヒの憂鬱",
         price: 0,
         maxprice: 5500
        },
        {
         title: "涼宮ハルヒの消失",
         price: 0,
         maxprice: 3500
        },
        {
         title: "特例措置団体ステラ女学院C3",
         price: 0,
         maxprice: 6000
        },
        {
         title: "ソードアート・オンライン",
         price: 0,
         maxprice: 20000
        },
        {
         title: "ファンタジスタドール",
         price: 0,
         maxprice: 6000
        },
      ],
      loginUser: null,
      random: parseInt(Math.random() * 100)
    }
  }
}
</script>

<style lang="scss">
@import "~assets/sass/splatnet2.scss";
@import "~assets/sass/salmonproject.scss";
</style>