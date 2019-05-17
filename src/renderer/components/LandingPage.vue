<template>
  <div class="wrapper">
    <Modal v-model="autoLoginModal" width="560">
        <p slot="header" style="color:#42b983;text-align:center">
            <Icon type="ios-information-circle"></Icon>
            <span>Auto sign in confirmation</span>
        </p>
        <div style="text-align:center">
            <h2>Saved username and password found keyed in before.</h2>
            <h2>Would you like to try to sign in using those credentials?</h2>
        </div>
        <div slot="footer">
            <Button @click="autoSignIn()" type="success" size="large" long>Sign in</Button>
        </div>
    </Modal>
    
    <div class="text-center absolute-center" v-if="!screensToggled">
      <div class="card bordered">
        <h1 class="header-big">Ketarn</h1>
        <h2>Already have an account - Sign In!</h2>
        <Input size="large" v-model="username" class="m10" placeholder="Username" style="width: 300px;" /><br>
        <Input size="large" v-model="password" class="m10" type="password" placeholder="Password" style="width: 300px;" /><br>
        <Button @click="signIn()" type="primary" class="m10">Sign In</Button><br>
        <Button @click="toggleScreens()" class="m10" type="dashed">Or Sign Up</Button><br>
      </div>
    </div>
    <div class="text-center absolute-center" v-if="screensToggled">
      <div class="card bordered">
        <h1 class="header-big">Ketarn</h1>
        <h2>Don't have an account yet - Sign Up!</h2>
        <Input size="large" v-model="username" class="m10" placeholder="Username" style="width: 300px;" /><br>
        <Input size="large" v-model="email" class="m10" placeholder="E-mail" style="width: 300px;" /><br>
        <Input size="large" v-model="password" class="m10" type="password" placeholder="Password" style="width: 300px;" /><br>
        <Button @click="signUp()" type="success" class="m10">Sign Up</Button><br>
        <Button @click="toggleScreens()" class="m10" type="dashed">Or Sign In</Button><br>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';
  import MD5 from 'crypto-js/md5'; // password is encrypted in md5 and stored to db
  var remote = require('electron').remote;

  const API = "http://localhost:1337/"; // url where are all our requests sent to

  export default {
    name: 'landing-page',
    components: {  },
    data() {
      return {
        screensToggled: false, // boolean that manages sign in & sign up screens
        username: '',
        email: '',
        password: '',
        userObject: {},
        autoLoginModal: false
      }
    },
    mounted() {

      remote.getCurrentWindow().setTitle('Ketarn');

      this.$Loading.start();

      axios.get(API + 'checkServer') // it simply returns a string. If we don't get a response => no connection
        .then(response => {
          this.$Loading.finish();
          if(this.loginDataSaved())
            this.autoLoginModal = true;
          // we don't need to know any response from the server
        })
        .catch(err =>{
          this.$Loading.error();
          this.showError('No connection to the server', 3600)
        }); // no connection
    },
    methods: {
      toggleScreens()
      {
        this.screensToggled = !this.screensToggled; // swaps between sign in & sign up screens
      },
      signIn()
      {

        this.$Loading.start();

        var authentication = {
          // auth information is taken either from textfields or from localstorage
          username: this.username || localStorage.getItem('username'),
          password: this.password || localStorage.getItem('password')
        }

        axios(API + 'loginRequest', {
          method: 'GET',
          auth: authentication // we send auth data in every request
        })
        .then(response => {
          if(response.data == null)
          {
             this.showError('Username or password is invalid', 2);
             localStorage.clear();
             this.$Loading.error();
          }
          
          else
          {
            this.$Loading.finish();
            this.showMessage('Signed in successfully', 2);
          } 

          this.userObject = response.data;

          localStorage.setItem('username', authentication.username);
          localStorage.setItem('password', authentication.password);

          this.$router.push({
            name: 'dashboard',
            params: {
              user: response.data
            }
          })

        }).catch(err => {
          this.showError('Username or password is invalid', 2);
          localStorage.clear();
          this.$Loading.error();
        });

        // clear textfields after pressing 'Sign In' button
        this.username = '';
        this.password = '';
        this.email = '';
      },
      signUp()
      {
        this.$Loading.start();

        axios.post(API + 'signUp', {
          username: this.username,
          password: this.password,
          email: this.email
        }).then(response => {
          if(response.data == true)
          {
            this.showMessage('Registered successfully. You can now sign in', 3);
            this.toggleScreens();
            this.$Loading.finish();
          }
          else 
          {
            this.showError('All fields are required!', 3);
            this.$Loading.error();
          }
        }).catch(err => {
            this.$Loading.error();
            this.showError('Error has occurred while registration', 3);
        });

        // clear textfields after pressing 'Sign In' button
        this.username = '';
        this.password = '';
        this.email = '';
      },
      showMessage(text, seconds)
      {
        this.$Message.success({content: text, duration: seconds});
      },
      showError(text, seconds)
      {
        this.$Message.error({content: text, duration: seconds});
      },
      areInputsValid()
      {
        return this.username != '' && this.password != '' && this.email != '';
      },
      autoSignIn() 
      {
        this.signIn();
      },
      loginDataSaved()
      {
        return localStorage.getItem('username') != null && localStorage.getItem('password') != null;
      }
    }
  }
</script>

<style>
  .wrapper {
    background: url('https://www.toptal.com/designers/subtlepatterns/patterns/leaves.png');
    height: 100vh;
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
    box-shadow: 10px 10px 32px -6px rgba(0,0,0,0.55);
  }

  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');
</style>