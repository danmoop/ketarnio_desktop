<template>
    <div>
        <ProjectNotificationModal ref="projectNotification" :project="this.project"></ProjectNotificationModal>
        <ViewProjectAdminsModal ref="adminsModal" :project="this.project"></ViewProjectAdminsModal>
        <ViewProjectMembersModal ref="membersModal" :project="this.project"></ViewProjectMembersModal>
        <ProjectInboxModal ref="projectInboxModal" :project="this.project"></ProjectInboxModal>
        <ProjectBudgetModal ref="projectBudgetModal" :project="this.project"></ProjectBudgetModal>

        <Row>
            <Col class="leftBlock text-center p10" span="5">
                <p class="title-p"><Icon type="ios-contact" />{{ user.username }}</p><br>
                <Divider />
                <Button @click="() => this.$refs.projectInboxModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p">
                        <Icon type="ios-mail-outline" /> Project Inbox 
                        <span v-html="badge()"></span>
                    </p>
                </Button><br>
                <Button @click="() => this.$refs.adminsModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="md-star-outline" /> View admins</p>
                </Button><br>
                <Button @click="() => this.$refs.membersModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-people-outline" /> View members</p>
                </Button><br>
                <Button class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-person-add-outline" /> Invite members</p>
                </Button><br>
                <Button @click="() => this.$refs.projectBudgetModal.toggle()" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-cash-outline" /> Set project budget</p>
                </Button><br>
                <Button class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="ios-chatboxes-outline" /> Project chat</p>
                </Button><br>
                <Divider />

                <Button @click="() => this.$router.push('/dashboard')" class="mt-5" type="text" long>
                    <p class="title-p"><Icon type="md-exit" /> Back</p>
                </Button><br>

            </Col>
            <Col span="19">
                <div>
                    <Row style="background: #16a085; padding: 10px;">
                        <Col span="12">
                            <h1 class="wh">{{ projectName }}</h1>
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
                            <p class="title-p">{{ project.members.length }}</p>
                        </Card>
                    </Col>
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Project budget</h2>
                            <p class="title-p">{{ formatString(project.budget) }}</p>
                        </Card>
                    </Col>
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Active tasks</h2>
                            <p class="title-p">{{ project.activeTasks.length }}</p>
                        </Card>
                    </Col>
                    <Col span="5">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Completed tasks</h2>
                            <p class="title-p">{{ project.completedTasks.length }}</p>
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
                            <p class="title-p" v-if="this.project.projectNotification == null">
                                This project has no notification set by an admin yet
                            </p>
                            <p class="title-p" v-if="project.projectNotification != null">
                                {{ project.projectNotification.text }}
                            </p>
                        </Card>
                    </Col>
                    <Col span="10">
                        <Card style="border: 1px solid #b8b8b8; height: 100%;">
                            <h2 slot="title">Project tasks</h2>
                            <p class="title-p" v-if="this.project.activeTasks.length == 0">
                                No active tasks yet
                            </p>
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
    import ProjectInboxModal from './modals/projectmodals/ProjectInboxModal';
    import ProjectBudgetModal from './modals/projectmodals/ProjectBudgetModal';

    export default {
        name: 'projectDashboard',
        components: {
            ProjectNotificationModal,
            ViewProjectAdminsModal,
            ViewProjectMembersModal,
            ProjectInboxModal,
            ProjectBudgetModal
        },
        data() {
            return {
                user: Object,
                projectName: '',
                project: Object
            }
        },
        mounted() {
            /*
                !!! UNCOMMENT LATER !!!

                this.user = this.$route.params.user; 
                this.projectName = this.$route.params.projectName;
            */

            // This are just for dev purposes so I don't restart app after making changes
            this.projectName = 'EA';   // DELETE
            this.signIn();             // DELETE
            // They will be deleted later after finishing the project dashboard

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
            setProject(project)
            {
                this.project = project;
            },
            formatString(str)
            {
                return str.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
            },
            showMessage(text, seconds)
            {
                this.$Message.success({content: text, duration: seconds});
            },
            showError(text, seconds)
            {
                this.$Message.error({content: text, duration: seconds});
            },
            badge() 
            {
                /*
                    actually badge shuold be displayed as following:
                    <Badge type="primary" :count="user.messages.length"></Badge>
                    but I use this function to modify text's font
                */

                var count = this.project.messages.length;

                return `
                <span class="ivu-badge"> 
                    <sup class="ivu-badge-count ivu-badge-count-alone ivu-badge-count-primary" style='font-family: "eina";'>${count}</sup>
                </span>`;
            }
        }
    }

</script>