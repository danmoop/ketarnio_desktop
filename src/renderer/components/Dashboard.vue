<template>
    <div>
        <router-link to="/">back</router-link>
        <h1> {{user}} </h1>
    </div>
</template>

<script>

var remote = require('electron').remote;

import axios from 'axios';

var API = 'http://localhost:1337/';

export default {
    name: 'dashboard',
    beforeMount() {
        remote.getCurrentWindow().setTitle("Ketarn - Dashboard");

        var authentication = {
          username: this.username || localStorage.getItem('username'),
          password: this.password || localStorage.getItem('password')
        }
        axios(API + 'loginRequest', {
          method: 'GET',
          auth: authentication
        })
        .then(response => { this.user = response.data });
    },
    data() {
        return {
            user: {}
        }
    }
}
</script>

<style>
    .text-center {
        text-align: center;
    }

    .mt-5 {
        margin-top: 20px;
    }
</style>
