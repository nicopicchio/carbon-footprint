<template>
  <div id="app">
    <div id="main-container">
      <img class="main-logo" src="./assets/logo.png" alt="logo" />
      <h1>Website carbon footprint checker</h1>
      <p class="subtitle">How much is your website impacting the planet?</p>
      <button v-if="findOutBtn" @click="handleFindOutBtn">
        Let's find out!
      </button>
      <input
        v-if="checkBtn && !findOutBtn"
        @input="handleUrlInput"
        class="input-url"
        type="text"
        placeholder="Enter domain"
        required
      />
      <button
        v-if="checkBtn && !findOutBtn"
        @click="handleCheckURLBtn"
        class="check-btn"
      >
        Check
      </button>
      <div v-if="!checkBtn && showReport" id="report-card">
        <h1>Carbon Footprint Report</h1>
        <h2 id="response-url">Website: {{ response.url }}</h2>
        <p id="bytes">Data on page load: {{ response.bytes }} Mb</p>
        <p>Your website is cleaner than</p>
        <h2 id="response-percentage">{{ response.cleanerThan }}%</h2>
        <p>of tested resources</p>
      </div>
      <div v-if="!showReport && showCO2Report" id="report-card">
        <h1>CO2 Report</h1>
        <h2 class="co2-report-text">CO2 from grid</h2>
        <h2 class="co2-report-text grid">{{ response.co2.grid }}g</h2>
        <h2 class="co2-report-text">CO2 from renewable sources</h2>
        <h2 class="co2-report-text renewable">{{ response.co2.renewable }}g</h2>
      </div>
      <p id="more-details" v-if="!showCO2Report && showReport" @click="showStats">
        Click for CO2 data
      </p>
      <p id="more-details" v-if="showCO2Report && !showReport" @click="showStats">
        Click for Carbon Footprint Report
      </p>
    </div>
    <footer>
      <p id="footer-text">
        Made by <a href="https://nicotech.dev" target="blank">Nico</a> for the
        ❤️ of the 🌍
      </p>
    </footer>
  </div>
</template>

<script>
import axios from "axios";
const bytesToMb = Math.pow(10, 6);
let userUrl = "";
export default {
  name: "App",
  data() {
    return {
      findOutBtn: true,
      checkBtn: true,
      showReport: false,
      showCO2Report: false,
      response: {
        url: "",
        bytes: "",
        cleanerThan: "",
        co2: {
          grid: "",
          renewable: "",
        },
      },
    };
  },
  methods: {
    handleFindOutBtn() {
      this.findOutBtn = false;
    },
    handleUrlInput(e) {
      userUrl = e.target.value;
    },
    handleCheckURLBtn(e) {
      e.preventDefault();
      this.checkBtn = false;
      this.showReport = true;
      axios
        .get(`http://localhost:8080/site?url=http%3A%2F%2F${userUrl}`)
        .then((response) => {
          (this.response.url = response.data.url),
            (this.response.bytes = (response.data.bytes / bytesToMb).toFixed(
              2
            )),
            (this.response.cleanerThan = (
              response.data.cleanerThan * 100
            ).toFixed(0));
          (this.response.co2.grid =
            response.data.statistics.co2.grid.grams.toFixed(2)),
            (this.response.co2.renewable =
              response.data.statistics.co2.renewable.grams.toFixed(2));
        })
        .catch((error) => console.log(error));
    },
    showStats() {
      this.showReport = !this.showReport;
      this.showCO2Report = !this.showCO2Report;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  height: 100vh;
}

body {
  background: #cce5ae;
  height: 100%;
  width: 100%;
  display: flex;
  place-items: center;
  justify-content: center;
}

p {
  margin: 1rem 0;
  font-size: 1.5rem;
}

a {
  text-decoration: none;
  color: #467920;
}

h1,
.subtitle {
  margin-bottom: 2rem;
}

button {
  border: none;
  border-radius: 14px;
  background-color: #2f5e32;
  padding: 1rem 2rem;
  color: white;
  font-size: 1rem;
  font-weight: bold;
  transition: 0.5s;
}

button:hover {
  background-color: white;
  color: #2f5e32;
}

footer {
  display: flex;
  justify-content: center;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#footer-text {
  font-size: 1rem;
}

#report-card {
  padding: 2rem;
  background-color: #fff;
  border-radius: 10px;
}

#bytes {
  font-size: 2rem;
  margin: 1rem 0;
}

#response-percentage {
  font-size: 5rem;
}

#more-details {
  font-size: 1rem;
}

.main-logo {
  width: 400px;
}

.input-url {
  display: block;
  margin: auto;
  padding: 0.5rem;
  width: 300px;
}

.co2-report-text {
  margin: 1rem 0;
}

.grid {
  color: rgb(236, 50, 50);
}

.renewable {
  color: #467920;
}

.check-btn {
  margin-top: 2rem;
}
</style>
