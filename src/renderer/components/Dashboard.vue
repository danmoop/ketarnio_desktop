<template>
    <div>
        <Row>
            <Col class="leftBlock text-center p10" span="4">
                <h2><Icon type="ios-contact" />{{user.username}}</h2>
                <Divider />
                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-brush-outline" /> Create Project</h2>
                </Button><br>

                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-mail-outline" /> Inbox</h2>
                </Button><br>

                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-build-outline" /> Edit Profile Info</h2>
                </Button><br>

                <Divider />
                <Button class="mt-5" type="text" long @click="logOut()">
                    <h2><Icon type="ios-log-out" /> Log Out</h2>
                </Button><br>
            </Col>
            <Col class="p10" span="20" style="background: #3f51b5;">
                <Row>
                    <Col span="12">
                        <h2 class="wh">Your dashboard</h2>
                    </Col>
                    <Col span="12">
                        <h2 class="wh text-right">Inbox (0)</h2>
                    </Col>
                </Row>
            </Col>
        </Row>
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

        this.signIn(); // delete then
    },
    data() {
        return {
            user: {}
        }
    },
    methods: {
        signIn()
        {
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
        logOut()
        {
            this.$router.push('/');
            localStorage.clear();
        }
    }
}
</script>

<style>
    .text-center {
        text-align: center;
    }

    .text-right {
        text-align: right;
    }

    .mt-5 {
        margin-top: 20px;
    }

    .wh {
        color: white;
    }

    .p5{
        padding: 5px;
    }

    .p10{
        padding: 10px;
    }

    .leftBlock {
        background: #fafafa;
        height: 100vh;
        box-shadow: 10px 10px 19px -11px rgba(0,0,0,0.25);
    }
</style>