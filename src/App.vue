<template>
  <div id="app">
    <div class="app_head">
      <h1>IRM Today</h1>
      <small>{{ datetime }}</small>
    </div>
    <Events/>
  </div>
</template>

<script>
import Events from './components/Events.vue'

export default {
  name: 'App',
  components: {
    Events
  },
  data() {
    return {
      datetime : ''
    }
  },
  mounted() {
    this.getCurrentDatetime()
  },
  methods: {
    getCurrentDatetime() {
      this.datetime = this.$moment().locale('fr').format('dddd Do MMMM YYYY - H:mm')
      setInterval(() => {
          this.datetime = this.$moment().locale('fr').format('dddd Do MMMM YYYY - H:mm')
      }, 20000)
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@600;800&display=swap');

body {
  background-color: #222f3e;
}

.app_head {
  margin-bottom: 30px;
}

h1 {
  color: white;
  margin-bottom: 0;
}

small {
  color: white;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin-left: 15%;
  margin-right: 15%;
}
</style>
