<template>
  <div>
    <div class="text-center absolute-center" v-if="!screensToggled">
      <div class="card bordered">
        <h1 class="header-big">Ketarn</h1>
        <h2>Already have an account - Sign In!</h2>
        <h2 style="color: green;">{{ msg }}</h2>
        <Input v-model="username" class="m10" placeholder="Username" style="width: 300px;" /><br>
        <Input v-model="password" class="m10" type="password" placeholder="Password" style="width: 300px;" /><br>
        <Button @click="signIn()" type="primary" class="m10">Sign In</Button><br>
        <Button @click="toggleScreens()" class="m10" type="dashed">Or Sign Up</Button><br>
      </div>
    </div>
    <div class="text-center absolute-center" v-if="screensToggled">
      <div class="card bordered">
        <h1 class="header-big">Ketarn</h1>
        <h2>Don't have an account yet - Sign Up!</h2>
        <Input v-model="username" class="m10" placeholder="Username" style="width: 300px;" /><br>
        <Input v-model="email" class="m10" placeholder="E-mail" style="width: 300px;" /><br>
        <Input v-model="password" class="m10" type="password" placeholder="Password" style="width: 300px;" /><br>
        <Button @click="signUp()" type="success" class="m10">Sign Up</Button><br>
        <Button @click="toggleScreens()" class="m10" type="dashed">Or Sign In</Button><br>
      </div>
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
        username: '',
        email: '',
        password: '',
        msg: ''
      }
    },
    methods: {
      toggleScreens()
      {
        this.screensToggled = !this.screensToggled;
      },
      signIn()
      {
        axios.post('http://localhost:1337/signIn', {
          username: this.username,
          password: this.password
        }).then(response => {
          this.msg = response.data
        }).catch(err => this.msg = 'fail')

        this.username = '';
        this.password = '';
        this.email = '';
      },
      signUp()
      {
        axios.post('http://localhost:1337/signUp', {
          username: this.username,
          password: this.password,
          email: this.email
        }).then(response => {
          if(response.data == 'success')
          {
            this.$Message.success({content: 'Registered successfully. You can now sign in', duration: 3});
            this.toggleScreens();
          }
          else 
            this.$Message.error({content: 'Unable to sign up', duration: 3});
        }).catch(err => this.$Message.error(err));

        this.username = '';
        this.password = '';
        this.email = '';
      }
    }
  }
</script>

<style>
  body {
    background: url('https://www.toptal.com/designers/subtlepatterns/patterns/leaves.png');
  }

  .text-center {
    text-align: center;
  }

  .m10 {
    margin: 10px;
  }

  .bordered {
    border: 1px solid #888;
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

  .card {
    background: white;
    width: 50%;
    margin: 0px auto;
    padding: 25px;
    box-shadow: 10px 10px 32px -6px rgba(0,0,0,0.75);
  }

  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');
</style>