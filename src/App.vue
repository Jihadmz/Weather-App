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
          : devcount === 1
          ? 'graybgc'
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
      <div v-else-if="hintcount == 0">
        Type the name of the city and then hit Enter
      </div>

      <div class="section">
        <li>
          <a
            href="mailto:jihadmahfouz8@gmail.com"
            v-if="devcount == 1"
            @click="hide()"
            >Send Feedback</a
          >
          <button v-if="devcount == 1" @click="onbtnclick()">Not now</button>
        </li>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
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
      api_key: process.env.VUE_APP_API_KEY,
      base_url: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      counter: 0,
      devcount: 0,
      hintcount: 0,
    };
  },
  mounted() {
    if (localStorage.counter) {
      this.counter = localStorage.counter;
    }
  },
  watch: {
    counter(newcounter) {
      localStorage.counter = newcounter;
    },
  },
  methods: {
    fetchingapi() {
      fetch(
        `${this.base_url}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
      )
        .then((result) => {
          if (result.status == 404) {
            alert(`${this.query} is not found, try a different city`);
          }
          return result.json();
        })
        .then((result) => {
          this.weather = result;
        });
      setTimeout(this.clearingquery, 1000);
      setTimeout(this.areyouenjoying, 20000);
      this.hintcount++;
    },

    getdate() {
      let date = new Date();
      return date.toDateString();
    },
    clearingquery() {
      this.query = "";
    },
    areyouenjoying() {
      if (this.counter == 0) {
        alert(
          `Are you enjoying our app, please take a minute and send your feedback to the developer`
        );
        this.devcount = 1;
        this.counter++;
      }
    },
    hide() {
      this.devcount = 0;
    },
    onbtnclick() {
      this.devcount = 0;
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
