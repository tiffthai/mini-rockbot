<template>
  <div id="header">
    <p>mini rockbot</p>
  </div>

  <div id="mainContent">
    <Tabs />
  </div>
</template>

<script>
import Tabs from "@/components/Tabs.vue";

export default {
  name: 'App',
  components: {
    Tabs
  }
}
</script>

<style>
html, body {
  margin: 0;
  width: 100%;
  height: 100%;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#header {
  background: #151617;
  color: white;
  height: 50px;
  width: 100%;
  position: fixed;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

#mainContent {
  height: calc(100% - 50px);
  margin-top: 50px;
}
</style>
