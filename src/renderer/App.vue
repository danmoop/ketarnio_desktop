<template>
  <div id="app">
    <transition name="fade">
      <router-view></router-view>
    </transition>
  </div>
</template>

<script>
  export default {
    name: 'ketarn'
  }
</script>

<style>
  @font-face {
    font-family: 'eina';
    src: url("./fonts/Eina.woff2") format("woff2");
  }

  * {
    user-select: none;
    font-family: 'eina';
  }

  .title-p {
    font-size: 19px;
  }

  .text-center {
    text-align: center;
  }

  .fz18 {
    font-size: 18px;
  }
</style>