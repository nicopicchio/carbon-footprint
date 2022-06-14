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
        placeholder="Enter domain here..."
        required
      />
      <button
        v-if="checkBtn && !findOutBtn"
        @click="handleCheckURLBtn"
        class="check-btn"
      >
        Check
      </button>
      <div v-if="!checkBtn" id="report-card">
        <h1>Your carbon footprint report</h1>
        <h2 id="response-url">{{ response.url }}</h2>
        <p id="bytes">Data: {{ response.bytes }} Mb</p>
        <p>Your website is cleaner than</p>
        <h2 id="response-percentage">{{ response.cleanerThan }}%</h2>
        <p>of tested resources</p>
      </div>
    </div>
    <footer>
      <p id="footer-text">
        Made by <a href="http://nicotech.dev">Nico</a> for the ❤️ of the 🌍
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
      response: {
        url: "",
        bytes: "",
        cleanerThan: "",
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
        })
        .catch((error) => console.log(error));
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
  margin: 2rem 0;
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

.main-logo {
  width: 400px;
}

.input-url {
  display: block;
  margin: auto;
  padding: 0.5rem;
  width: 300px;
}

.check-btn {
  margin-top: 2rem;
}
</style>
