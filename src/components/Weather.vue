<template>
  <div class="weather">
    <h1>{{ feelslike }} °{{ unit }}</h1>

    <div class="weather-bot">
      <div>
        <p class="light">Weather in</p>
        <p class="strong">{{ locFull }}</p>
      </div>
      <div class="state">
        <img :src="image_url" />
        <p class="light">{{ condition_text }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  props: {
    loc: {
      type: String,
      default: 'idk',
    },
    unit: {
      type: String,
      default: 'c',
    },
  },
  data() {
    return {
      feelslike: '-',
      locFull: '',
      condition_text: '',
      image_url: '',
      API_KEY: '',
    }
  },
  mounted() {
    this.getWeather()
    this.API_KEY = localStorage.getItem('apikey')
  },
  methods: {
    getWeather() {
      axios
        .get(
          `http://api.weatherapi.com/v1/current.json?key=${this.API_KEY}&q=${this.loc}&aqi=yes`
        )
        .then((response) => {
          if (this.unit.toLowerCase() == 'c') {
            this.feelslike = `${response.data.current.temp_c}`
          } else {
            this.feelslike = `${response.data.current.temp_f}`
          }
          this.locFull = `${response.data.location.name}, ${response.data.location.country}`
          this.condition_text = response.data.current.condition.text
          console.log(response.data.current.condition.icon)
          this.image_url = `http://${response.data.current.condition.icon}`
        })
    },
  },
  watch: {
    loc() {
      this.getWeather()
    },
    unit() {
      this.getWeather()
    },
  },
}
</script>

<style scoped>
.weather-bot {
  display: flex;
  align-items: flex-end;
  width: 40vw;
  justify-content: space-between;
}

.state {
  display: flex;
  align-items: center;
  flex-direction: column;
}

.weather {
  font-size: 1.4em;
}

img {
  width: 2.5em;
}

.state {
  padding-left: 10vw;
  text-align: center;
}
</style>
