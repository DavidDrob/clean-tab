<template>
  <div
    class="settings-modal"
    :style="`background-color: rgba(${modalColor}, ${alpha})`"
  >
    <div class="ui">
      <form>
        <h4>Color Settings</h4>

        <div class="setting">
          <div class="clr">
            <h6>Main Color</h6>
            <input @input="saveColor" type="color" v-model="color" />
          </div>

          <div class="setting-horizontal">
            <div class="clr">
              <h6>Settings Color</h6>
              <input
                @input="saveModalColor"
                type="color"
                v-model="modalColorHex"
              />
            </div>
            <div class="clr">
              <h6>Transparency</h6>
              <input
                type="range"
                class="slider"
                min="1"
                v-model="getAlpha"
                @input="changeAlpha"
              />
            </div>
          </div>
        </div>
      </form>
    </div>
    <div class="background-settings">
      <form>
        <h4 class="middle-child">Background Settings</h4>
        <h6>Background Type</h6>
        <select @change="updateBg" name="bgOption" v-model="bgOption">
          <option value="" disabled selected>
            Select if you want to use an Image or a Color for the Background
          </option>
          <option v-for="set in bgSettings" :value="set.type" :key="set.id">
            {{ set.name }}
          </option>
        </select>

        <div v-if="bgOption == 'i'" class="bg-opt">
          <h6>Background Image</h6>
          <input type="file" @change="uploadBackground($event)" />
        </div>

        <div v-if="bgOption == 'c'" class="bg-opt">
          <div class="clr">
            <h6>Background Color</h6>
            <input @input="saveBgColor" type="color" v-model="bgColor" />
          </div>
        </div>
      </form>
    </div>
    <div class="test-ima"></div>
    <div class="weather">
      <form @submit.prevent="saveSettings">
        <h4>Weather Settings</h4>
        <div class="setting-horizontal">
          <div class="setting">
            <h5 class="weather-text" for="location">Your Location</h5>
            <input
              class="text"
              v-model="loc"
              type="text"
              name="location"
              placeholder="Location"
            />
          </div>
          <div class="setting">
            <h5 class="weather-text" for="apikey">
              Weatherapi Key
              <span
                ><a href="https://www.weatherapi.com/signup.aspx">?</a></span
              >
            </h5>
            <input
              class="text api"
              v-model="apikey"
              type="text"
              name="apik"
              placeholder="Api Key"
            />
          </div>
        </div>
        <div class="setting">
          <h5 for="unit">Unit (C/F)</h5>
          <select name="unit" v-model="unit">
            <option value="" disabled selected>Select the unit</option>
            <option v-for="uni in unitOptions" :value="uni.unit" :key="uni.id">
              {{ uni.name }}
            </option>
          </select>
        </div>
      </form>
    </div>
    <input
      type="submit"
      class="btn"
      value="Save Weather Settings"
      @click.prevent="saveSettings"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      loc: '',
      unit: '',
      unitOptions: {
        celc: { unit: 'C', name: 'Celsius', id: 0 },
        fahr: { unit: 'F', name: 'Fahrenheit', id: 1 },
      },
      color: 'FC5608',
      bgColor: '',
      bgImgSrc: '',
      bgOption: '',
      bgSettings: {
        img: { type: 'i', name: 'Image', id: 0 },
        clr: { type: 'c', name: 'Color', id: 1 },
      },
      alpha: 0.6,
      modalColor: '111,125,165',
      modalColorHex: '#000000',
      apikey: '',
    }
  },
  methods: {
    saveSettings() {
      if (this.loc != '') {
        localStorage.setItem('location', this.loc)
      } else {
        localStorage.setItem('location', 'Vienna')
      }
      if (this.unit != '') {
        localStorage.setItem('unit', this.unit)
      } else {
        localStorage.setItem('unit', 'C')
      }
      localStorage.setItem('apikey', this.apikey)
      this.$emit('update-storage')
    },
    changeAlpha(e) {
      this.alpha = e.target.value / 100
      localStorage.setItem('alpha', e.target.value / 100)
    },
    saveColor() {
      if (this.color == '') {
        this.color = '#000000'
      }
      localStorage.setItem('color', this.color)

      this.$emit('change-color-settings')
    },
    saveBgColor() {
      if (this.bgColor == '') {
        this.bgColor = '#000000'
      }
      localStorage.setItem('bg-color', this.bgColor)

      this.$emit('change-bg-color-settings')
    },
    saveModalColor(e) {
      const hexToRGB = (hex) => {
        let r = 0,
          g = 0,
          b = 0
        // handling 3 digit hex
        if (hex.length == 4) {
          r = '0x' + hex[1] + hex[1]
          g = '0x' + hex[2] + hex[2]
          b = '0x' + hex[3] + hex[3]
          // handling 6 digit hex
        } else if (hex.length == 7) {
          r = '0x' + hex[1] + hex[2]
          g = '0x' + hex[3] + hex[4]
          b = '0x' + hex[5] + hex[6]
        }

        return {
          red: +r,
          green: +g,
          blue: +b,
        }
      }
      this.modalColor = `${hexToRGB(e.target.value).red},${
        hexToRGB(e.target.value).green
      },${hexToRGB(e.target.value).blue}`
      localStorage.setItem('modal-color', this.modalColor)
    },
    updateBg() {
      localStorage.setItem('bg-option', this.bgOption)
      this.$emit('check-bg-option')
    },
    uploadBackground(e) {
      const reader = new FileReader()
      reader.addEventListener('load', () => {
        localStorage.setItem('bg', reader.result)
        this.$emit('update-background')
      })
      reader.readAsDataURL(e.target.files[0])
    },
  },
  computed: {
    getAlpha() {
      if (this.alpha === 0) {
        return 0.6
      } else {
        return this.alpha * 100
      }
    },
  },
  mounted: function () {
    this.color = localStorage.getItem('color')
    this.bgColor = localStorage.getItem('bg-color')
    this.bgOption = localStorage.getItem('bg-option')
    this.alpha = localStorage.getItem('alpha')
    this.modalColor = localStorage.getItem('modal-color')
    this.apikey = localStorage.getItem('apikey')
  },
}
</script>

<style scoped>
.settings-modal {
  height: 100vh;
  padding-top: 5vh;
  padding: 5vh 0 0 3vw;
}

h4 {
  font-size: 1.5em;
  padding: 60px 00px 00px 0px;
}

.middle-child {
  margin-top: 0;
  padding-top: 0;
}

.background-settings input {
  border: none;
}

.setting-horizontal {
  display: flex;
  width: 15vw;
  justify-content: space-between;
  margin-top: 1vh;
}

.setting {
  margin-bottom: 4vh;
  display: flex;
  flex-direction: column;
  width: 35%;
}

h5 {
  padding-bottom: 2vh;
}

input,
select {
  background: none;
  border: none;
  padding-bottom: 8px;
  border-bottom: 2px solid #e5e5e5;
}
input::placeholder {
  color: #e5e5e5;
}

input[type='file'] {
  position: absolute;
  font-size: 17px;
  color: #e5e5e5;
  border: none;
  outline: none;
}

input[type='text'] {
  color: #e5e5e5;
}

select {
  border: 0;
  border-bottom: 2px solid #e5e5e5;
  color: #e5e5e5;
}

.btn {
  background-color: #e5e5e5;
  border: none;
  padding: 12px 19px;
  text-align: center;
  text-decoration: none;
  cursor: pointer;
}

.clr input {
  border: none;
  height: 5vh;
  width: 4vw;
}

@media only screen and (max-width: 1440px) {
  .clr input {
    width: 6vw;
  }
}

h6 {
  margin-top: 1vh;
}

.api {
  width: 15vw;
}

span,
a {
  font-size: 10px;
  color: #e5e5e5;
}

.weather-text {
  width: 15vw;
}
</style>