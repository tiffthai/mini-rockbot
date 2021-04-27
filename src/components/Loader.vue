<template>  
    <div class="wrap">
        <div class="box1"></div>
        <div class="box2"></div>
        <div class="box3"></div>
    </div>
</template>

<script>
export default {
    name: 'Loader'
}
</script>

<style scoped>
.wrap {
  position: absolute;
  bottom: 50%;
  left: 50%;
  margin-right: -50%;
  transform: translateX(-50%) rotate(180deg);

}

.box1,
.box2,
.box3 {
  float:left;
  margin: 0 5px;
  height: 20px;
  width: 20px;
  background: #007EE4;
  /*   animation */
  animation: loading;
  animation-duration: 2s;
  animation-timing-function: ease-in-out;
  animation-iteration-count: infinite;
}

.box3 {
  animation-delay: 1s;
}

.box2 {
  animation-delay: 1.3s;
}

.box1 {
  animation-delay: 1.5s;
}

@keyframes loading {
  20% {
    height:40px;
  }
  50%{
    height: 20px;
  }

}
</style>