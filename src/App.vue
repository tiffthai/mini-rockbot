<template>
  <div id="header">
    <p>mini rockbot</p>
  </div>

  <div id="mainContent">
    <Tabs />
  </div>
</template>

<script>
import Tabs from '@/components/Tabs.vue';

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
  font-family: 'Lato', sans-serif;
}

p {
  margin: 0;
  user-select: none;
  letter-spacing: ;
}

#app {
  height: 100%;
  display: flex;
  flex-direction: column;
}

#header {
  background: #151617;
  color: white;
  height: 50px;
  width: 100%;
  position: fixed;
  display: flex;
  align-items: center;
  justify-content: center;
}

#mainContent {
  height: calc(100% - 50px);
  margin-top: 50px;
}

.sectionLabel {
  text-decoration: underline;
  padding: 8px 12px;
}
</style>
