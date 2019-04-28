<template>
    <div>

        <ProjectNotificationModal ref="projectNotification" :project="this.project"></ProjectNotificationModal>
        <ViewProjectAdminsModal ref="adminsModal" :project="this.project"></ViewProjectAdminsModal>
        <ViewProjectMembersModal ref="membersModal" :project="this.project"></ViewProjectMembersModal>

        <Row>
            <Col class="leftBlock text-center p10" span="4">
                <h2><Icon type="ios-contact" />{{ user.username }}</h2><br>
                <Divider />
                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-mail-outline" /> Project Inbox ({{ user.messages.length }})</h2>
                </Button><br>
                <Button @click="() => this.$refs.adminsModal.toggle()" class="mt-5" type="text" long>
                    <h2><Icon type="md-star-outline" /> View admins</h2>
                </Button><br>
                <Button @click="() => this.$refs.membersModal.toggle()" class="mt-5" type="text" long>
                    <h2><Icon type="ios-people-outline" /> View members</h2>
                </Button><br>
                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-person-add-outline" /> Invite members</h2>
                </Button><br>
                <Button class="mt-5" type="text" long>
                    <h3><Icon type="ios-cash-outline" /> Set project budget</h3>
                </Button><br>
                <Button class="mt-5" type="text" long>
                    <h2><Icon type="ios-chatboxes-outline" /> Project chat</h2>
                </Button><br>
                <Divider />

                <Button class="mt-5" type="text" long>
                    <h2><Icon type="md-exit" /> <router-link to="/dashboard">Back</router-link></h2>
                </Button><br>

            </Col>
            <Col span="20">
                <div>
                    <Row style="background: #16a085; padding: 10px;">
                        <Col span="12">
                            <h2 class="wh">{{ projectName }}</h2>
                        </Col>
                        <Col span="12" class="text-right">
                            <Button @click="refreshProject(true)"><Icon type="ios-refresh-circle-outline" /> Refresh</Button>
                        </Col>
                    </Row>
                </div>
            
                <Divider />

                <Row type="flex" justify="space-around" class="code-row-bg text-center">
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Total members</h2>
                            <h2>{{ project.members.length }}</h2>
                        </Card>
                    </Col>
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Project budget</h2>
                            <h2>{{ project.budget }}</h2>
                        </Card>
                    </Col>
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Active tasks</h2>
                            <h2>{{ project.activeTasks.length }}</h2>
                        </Card>
                    </Col>
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Completed tasks</h2>
                            <h2>{{ project.completedTasks.length }}</h2>
                        </Card>
                    </Col>
                </Row>

                <Divider />
                
                <Row type="flex" justify="space-around" class="code-row-bg text-center">
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Project notification 
                                <span v-if="this.project.projectNotification != null">
                                    by <u>{{ project.projectNotification.author }}</u> <br>
                                    set on <u>{{ project.projectNotification.timeStamp }}</u>
                                </span> <br><br>
                                <Button @click="() => this.$refs.projectNotification.toggle()" v-if="this.project.admins.includes(this.user.username)">Edit</Button>
                            </h2>
                            <h2 v-if="this.project.projectNotification == null">
                                This project has no notification set by an admin yet
                            </h2>
                            <h2 v-if="project.projectNotification != null">
                                {{ project.projectNotification.text }}
                            </h2>
                        </Card>
                    </Col>
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Project tasks</h2>
                            <h2 v-if="this.project.activeTasks.length == 0">
                                No active tasks yet
                            </h2>
                        </Card>
                    </Col>
                </Row>
            </Col>
        </Row>
    </div>
</template>

<script>

    import axios from 'axios';
    var remote = require('electron').remote;
    var API = "http://localhost:1337/";

    import ProjectNotificationModal from './modals/projectmodals/ProjectNotificationModal';
    import ViewProjectAdminsModal from './modals/projectmodals/ViewProjectAdminsModal';
    import ViewProjectMembersModal from './modals/projectmodals/ViewProjectMembersModal';

    export default {
        name: 'projectDashboard',
        components: {
            ProjectNotificationModal,
            ViewProjectAdminsModal,
            ViewProjectMembersModal
        },
        data() {
            return {
                user: Object,
                projectName: '',
                project: Object
            }
        },
        mounted() {
            //this.user = this.$route.params.user;
            //this.projectName = this.$route.params.projectName;


            this.projectName = 'EA'; // DELETE
            this.signIn(); // DELETE

            remote.getCurrentWindow().setTitle('Ketarn - ' + this.projectName);

            this.refreshProject(false);
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
            refreshProject(messageShown)
            {
                var authentication = {
                    username: this.username || localStorage.getItem('username'),
                    password: this.password || localStorage.getItem('password')
                }
                axios(API + 'getProject', {
                    method: 'post',
                    data: { text: this.projectName },
                    auth: authentication
                })
                .then(response => { 
                    this.project = response.data;

                    console.log(this.project);
                });

                if(messageShown)
                    this.showMessage('Done!', 1.5);
            },
            showMessage(text, seconds)
            {
                this.$Message.success({content: text, duration: seconds});
            },
            showError(text, seconds)
            {
                this.$Message.error({content: text, duration: seconds});
            }
        }
    }

</script>