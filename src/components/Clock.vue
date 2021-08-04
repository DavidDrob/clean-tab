<template>
  <h1>{{ hour }}:{{ minute }}:{{ second }}</h1>
  <h2>{{ day }}.{{ month }}.{{ year }}</h2>
</template>

<script>
export default {
  data() {
    return {
      hour: Number,
      minute: Number,
      second: Number,
      day: Number,
      month: Number,
      year: Number,
    }
  },
  methods: {
    getTime() {
      let time = new Date()
      this.hour = time.getHours()
      this.minute = time.getMinutes()
      this.second = time.getSeconds()
      this.day = time.getDate()
      this.month = time.getMonth() + 1
      this.year = time.getFullYear()
    },
  },
  mounted() {
    this.getTime()
    setInterval(() => {
      this.getTime()
    }, 1000)
  },
}
</script>

<style scoped>
h1 {
  font-size: 4.5em;
  margin: 0;
}
h2 {
  font-size: 3em;
  margin: 0;
}
</style>