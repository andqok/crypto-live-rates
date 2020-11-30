<template>
  <div id="nav">
    <router-link to="/">USD rates</router-link> |
    <router-link to="/about">Page 2</router-link>
  </div>
  <router-view/>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  height: 3rem;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
  line-height: 3rem;
}

#nav a.router-link-exact-active {
  color: #77e9b8;
}

.home {
  display: grid;
  grid-template-columns: 400px 1fr;
}

html, body, #app {
  height: 100%;
}

.home {
  height: calc(100% - 3rem);
}

body {
  background-image: linear-gradient(to right, #2c3e50, #42b983);
}

.crypto-list {
  overflow-y: auto;
  display: grid;
  grid-gap: 1rem;
  background: transparent;
  padding: .2rem 1rem;
}


</style>
