<template>
  <div id="app">
    <img class="main-logo" src="./assets/logo.png" alt="logo" />
    <h1>Website carbon footprint checker</h1>
    <p>How much is your website impacting the planet?</p>
    <button v-if="findOutBtn" @click="handleFindOutBtn">Let's find out!</button>
    <input
      v-if="!findOutBtn"
      class="input-url"
      type="text"
      placeholder="Enter URL here..."
      required
    />
    <button v-if="!findOutBtn" @click="handleCheckURLBtn" class="check-btn">
      Check
    </button>
    <div v-if="!findOutBtn" id="report-card">
      <h1>Your carbon footprint report</h1>
      <h2>{{ response.url }}</h2>
      <p>bytes {{ response.bytes }}</p>
      <p>Your website is cleaner than</p>
      <h2>{{ response.cleanerThan }}%</h2>
      <p>of tested resources</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      findOutBtn: true,
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
    handleCheckURLBtn() {
      axios
        .get("http://localhost:8080/site?url=http%3A%2F%2Fwww.nationalgeographic.com")
        .then((response) => {
          (this.response.url = response.data.url),
            (this.response.bytes = response.data.bytes),
            (this.response.cleanerThan = (response.data.cleanerThan * 100).toFixed(0));
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

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#report-card {
  padding: 2rem;
  background-color: #fff;
  border-radius: 10px;
}

h1,
p {
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
}

button:hover {
  background-color: white;
  color: #2f5e32;
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
