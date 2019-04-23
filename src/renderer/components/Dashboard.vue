<template>
    <div>
        <Modal v-model="NoteModal" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-brush-outline" />
                <span>Change your notes</span>
            </p>
            <div style="text-align:center">
                <Input type="text" v-model="user.note" placeholder="Notes">
                </Input>
            </div>
            <div slot="footer">
                <Button @click="setNote()" type="success" size="large">Save</Button>
            </div>
        </Modal>
        <Row>
            <Col class="leftBlock text-center p10" span="4">
                <h2><Icon type="ios-contact" />{{user.username}}</h2>
                <Divider />
                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-brush-outline" /> Create Project</h2>
                </Button><br>

                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-mail-outline" /> Inbox ({{user.messages.length}})</h2>
                </Button><br>

                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-build-outline" /> Edit Profile Info</h2>
                </Button><br>

                <Divider />
                <Button class="mt-5" type="text" long @click="logOut()">
                    <h2><Icon type="ios-log-out" /> Log Out</h2>
                </Button><br>
            </Col>
            <Col span="20">
                <div>
                    <Row style="background: #3f51b5; padding: 10px;">
                        <Col span="12">
                            <h2 class="wh">Your dashboard</h2>
                        </Col>
                        <Col span="12">
                            <h2 class="wh text-right">Inbox ({{user.messages.length}})</h2>
                        </Col>
                    </Row>
                </div>
            
                <Divider />

                <Row type="flex" justify="space-around" class="code-row-bg text-center">
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8;">
                            <h1 slot="title">Your projects</h1>
                            <p v-if="this.user.projects.length == 0">You have no projects yet</p>
                        </Card>
                    </Col>
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8;">
                            <h1 slot="title">Your tasks</h1>
                            <p v-if="this.user.tasks.length == 0">You have no tasks yet</p>
                            <Row class="text-center" v-if="this.user.tasks.length != 0">
                                <Col span="12">
                                    <h3>Completed tasks: 0</h3>
                                </Col>
                                <Col span="12">
                                    <h3>Work success: 0%</h3>
                                </Col>
                            </Row>
                        </Card>
                    </Col>
                </Row>

                <Divider />

                <Card class="text-center card-border" style="width: 70%; margin: 0px auto; border: 1px solid #b8b8b8;">
                    <h1 slot="title">Your notes</h1>
                    <Button @click="() => this.NoteModal = true">Edit</Button> <br><br>
                    <p class="fz20" v-if="this.user.note == ''">You have no notes yet</p>
                    <p class="fz20">{{ user.note }}</p>
                </Card>
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
            user: {},
            NoteModal: false
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
                method: 'get',
                auth: authentication
            })
            .then(response => { 
                this.user = response.data;
                console.log(this.user);
            });
        },
        setNote()
        {
            var authentication = {
                username: localStorage.getItem('username'),
                password: localStorage.getItem('password')
            }

            axios(API + "setUserNote", {
                method: 'post',
                data: { text: this.user.note },
                auth: authentication
            }).then(response => {
                // response doesn't matter
                this.NoteModal = false;
            }).catch(err => console.log(err));
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

    .fz20 {
        font-size: 20px;
    }
</style>