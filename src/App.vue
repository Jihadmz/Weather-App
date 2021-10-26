<template>
  <div v-if="sizing == true"><h1>This device is not supported</h1></div>

  <div v-else>
    <div
      id="weather"
      :class="
        typeof weather.main != 'undefined' &&
        weather.weather[0].main.toString().toLowerCase() == 'clouds'
          ? 'cloudyimg'
          : typeof weather.main != 'undefined' &&
            weather.weather[0].main.toString().toLowerCase() == 'rain'
          ? 'rainyimg'
          : ''
      "
    >
      <input
        type="text"
        placeholder="Search..."
        @keypress.enter="fetchingapi()"
        v-model="query"
        spellcheck="false"
      />

      <br />
      <br />
      <div v-if="typeof weather.main != 'undefined'">
        <transition>
          <h2 id="countryname">{{ weather.name }},{{ weather.sys.country }}</h2>
        </transition>
        <h4>{{ getdate() }}</h4>
        <br />

        <div class="temp">
          <h3 id="tempdeg">{{ Math.round(weather.main.temp) }}°c</h3>
        </div>

        <div class="rainorsunny">{{ weather.weather[0].main }}</div>
      </div>

      <div v-else>
        Type the name of the city and then hit
        <div class="hint">
          <span id="firstdelimeter">\</span><span id="seconddelimeter">/</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent, onMounted } from "vue";
import "./styles/style.scss";

export default defineComponent({
  setup() {
    onMounted(() => {
      let width =
        window.innerWidth ||
        document.documentElement.clientWidth ||
        document.body.clientWidth;
      let weather = document.getElementById("weather");
      if (width > 450) {
        alert(
          "Your device is not supported by my website, browse it on your phone"
        );
        weather?.remove();
      }
    });
  },
  data() {
    return {
      api_key: "56eecbb8c1736f3ccb3b5ab3d8be00d3",
      base_url: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
    };
  },
  methods: {
    async fetchingapi() {
      const promise = await fetch(
        `${this.base_url}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
      )
        .then((result) => {
          if (result.status == 404) {
            alert("You entered wrong city name!");
          }
          return result.json();
        })
        .then((result) => {
          this.weather = result;
        });
      this.query = "";
      return promise;
    },

    getdate() {
      let date = new Date();
      return date.toDateString();
    },
  },
  computed: {
    sizing() {
      if (window.innerWidth > 450) {
        return true;
      } else return false;
    },
  },
});
</script>
