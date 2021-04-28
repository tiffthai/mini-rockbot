<template>
    <div id="loading"></div>
</template>

<script>
export default {
    name: 'Spinner'
}
</script>

<style scoped>
#loading {
  display: inline-block;
  width: 18px;
  height: 18px;
  border: 1px solid rgba(255,255,255,.3);
  border-radius: 50%;
  border-top-color: #007EE4;
  animation: spin 1s ease-in-out infinite;
  -webkit-animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  to { -webkit-transform: rotate(360deg); }
}
@-webkit-keyframes spin {
  to { -webkit-transform: rotate(360deg); }
}
</style>