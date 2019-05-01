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
 *{
   user-select: none;
 }

 .text-center {
   text-align: center;
 }

 .fz18 {
   font-size: 18px;
 }
</style>
