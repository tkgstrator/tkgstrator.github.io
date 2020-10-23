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
            >あなたの現在の順位: {{ parseInt(Math.random() * 100) }} 位!!</span
          >
        </h3>
      </div>
      <div class="coop-content-list-wrapper">
        <div id="coop-content-list">
          <table>
            <thead>
              <tr>
                <th>Nickname</th>
                <th>Score</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(user, index) in users">
                <td>{{ user.uid }}</td>
                <td>{{ user.value }}</td>
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
  mounted: function () {
    console.log("COMPUTED!")
    const userRef = this.$fireStore.collection("users")
    try {
      const userRefs = userRef.get().then(snapshot => {
        snapshot.forEach(document => {
          console.log(document.data())
          this.users.push(document.data())
        })
      })
    } catch (error) {
      alert(error)
      return
    }
    console.log(this.users)
  },
  methods: {
  },
  data() {
    return {
      users: [],
      random: parseInt(Math.random() * 100)
    }
  },
}
</script>

<style lang="scss">
@import "~assets/sass/splatnet2.scss";
@import "~assets/sass/salmonproject.scss";
</style>