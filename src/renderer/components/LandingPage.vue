<template>
  <div>
    <div class="text-center absolute-center" v-if="!screensToggled">
      <h1 class="header-big">Ketarn</h1>
      <h2>Already have an account - Sign In!</h2>
      <Input class="m10" placeholder="Username" style="width: 300px;" /><br>
      <Input class="m10" type="password" placeholder="Password" style="width: 300px;" /><br>
      <Button class="m10">Sign In</Button><br>
      <Button @click="toggleScreens()" class="m10" type="dashed">Or Sign Up</Button><br>
    </div>
    
    <div class="text-center absolute-center" v-if="screensToggled">
      <h1 class="header-big">Ketarn</h1>
      <h2>Don't have an account yet - Sign Up!</h2>
      <Input class="m10" placeholder="Username" style="width: 300px;" /><br>
      <Input class="m10" placeholder="E-mail" style="width: 300px;" /><br>
      <Input class="m10" type="password" placeholder="Password" style="width: 300px;" /><br>
      <Button class="m10">Sign Up</Button><br>
      <Button @click="toggleScreens()" class="m10" type="dashed">Or Sign In</Button><br>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'landing-page',
    components: {  },
    data() {
      return {
        screensToggled: false,
      }
    },
    methods: {
      toggleScreens()
      {
        this.screensToggled = !this.screensToggled;
      }
    }
  }
</script>

<style>
  .text-center {
    text-align: center;
  }

  .m10 {
    margin: 10px;
  }

  .header-big {
    font-size: 35px;
  }

  .absolute-center {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 100%;
  }

  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');
</style>