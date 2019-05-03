<template>
    <div>
        <ProjectCreationModal ref="projectModal" :user="this.user"></ProjectCreationModal>
        <SetNoteModal ref="NoteModal" :user="this.user"></SetNoteModal>
        <UserInbox ref="userInboxModal" :user="this.user"></UserInbox>
        <SendMessageModal ref="sendMessageModal"></SendMessageModal>
        
        <Row>
            <Col class="leftBlock text-center p10" span="4">
                <h2><Icon type="ios-contact" />{{ user.username }}</h2><br>
                <h3><Icon type="ios-mail-outline" />{{ user.email }}</h3>
                <Divider />
                <Button @click="() => this.$refs.projectModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-brush-outline" /> Create Project</p>
                </Button><br>

                <Button @click="() => this.$refs.userInboxModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-mail-outline" /> Inbox <span v-html="badge()"></span></p>
                </Button><br>

                <Button @click="() => this.$refs.sendMessageModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="md-send" /> Send a message</p>
                </Button><br>
                

                <Button class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-build-outline" /> Edit Profile Info</p>
                </Button><br>

                <Divider />
                <Button class="mt-5" type="text" long @click="logOut()">
                    <p class="title-p"><Icon type="ios-log-out" /> Log Out</p>
                </Button><br>
            </Col>
            <Col span="20">
                <div>
                    <Row style="background: #3f51b5; padding: 10px;">
                        <Col span="12">
                            <h2 class="wh">Your dashboard</h2>
                        </Col>
                        <Col span="12" class="text-right">
                            <Button @click="refreshProfile(true)"><Icon type="ios-refresh-circle-outline" /> Refresh</Button>
                        </Col>
                    </Row>
                </div>
            
                <Divider />

                <Row type="flex" justify="space-around" class="code-row-bg text-center">
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8;">
                            <h1 slot="title">Your projects</h1>
                            <p class="fz20" v-if="this.user.projects.length == 0">You have no projects yet</p>
                            <ul>
                                <li v-for="project in this.user.projects" style="list-style: none; margin-top: 10px;">
                                    <Button @click="openProject(project)"><h3>{{ project }}</h3></Button>
                                </li>
                            </ul>
                        </Card>
                    </Col>
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h1 slot="title">Your tasks</h1>
                            <p class="fz20" v-if="this.user.tasks.length == 0">You have no tasks yet</p>
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
                    <Button @click="() => this.$refs.NoteModal.toggle()">Edit</Button> <br><br>
                    <p class="fz20" v-if="this.user.note == ''">You have no notes yet</p>
                    <p class="fz20">{{ user.note }}</p>
                </Card>
            </Col>
        </Row>
    </div>
</template>d

<script>

    var remote = require('electron').remote;

    import axios from 'axios';
    import ProjectCreationModal from './modals/usermodals/ProjectCreationModal';
    import SetNoteModal from './modals/usermodals/SetNoteModal';
    import UserInbox from './modals/usermodals/UserInbox';
    import SendMessageModal from './modals/usermodals/SendMessageModal';

    var API = 'http://localhost:1337/';

    export default {
        name: 'dashboard',
        components: {
            ProjectCreationModal,
            SetNoteModal,
            UserInbox,
            SendMessageModal
        },
        beforeMount() {
            remote.getCurrentWindow().setTitle("Ketarn - Dashboard");

            this.signIn(); // delete then
            //this.user = this.$route.params.user;
        },
        data() {
            return {
                user: Object,
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
                });
            },
            logOut()
            {
                this.$router.push('/');
                localStorage.clear();
            },
            refreshProfile(isMessageShown)
            {
                this.$Loading.start();

                var authentication = {
                    username: localStorage.getItem('username'),
                    password: localStorage.getItem('password')
                }

                axios(API + "refreshUserProfile", {
                    method: 'get',
                    auth: authentication
                }).then(response => {
                    this.user = response.data;
                    this.$Loading.finish();
    
                    if(isMessageShown)
                        this.showMessage('Done!', 1.5);
                }).catch(err => {
                    this.showError("Unable to refresh profile", 1.5);
                    this.$Loading.error();
                });
            },
            showMessage(text, seconds)
            {
                this.$Message.success({content: text, duration: seconds});
            },
            showError(text, seconds)
            {
                this.$Message.error({content: text, duration: seconds});
            },
            openProject(project)
            {
                this.$router.push({
                    name: 'projectDashboard',
                    params: {
                        user: this.user,
                        projectName: project
                    }
                });
            },
            setUser(user)
            {
                this.user = user;
            },
            badge() 
            {
                /*
                    actually badge shuold be displayed as following:
                    <Badge type="primary" :count="user.messages.length"></Badge>
                    but I use this function to modify text's font
                */

                var count = this.user.messages.length;

                return `
                <span class="ivu-badge"> 
                    <sup class="ivu-badge-count ivu-badge-count-alone ivu-badge-count-primary" style='font-family: "eina";'>${count}</sup>
                </span>`;
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