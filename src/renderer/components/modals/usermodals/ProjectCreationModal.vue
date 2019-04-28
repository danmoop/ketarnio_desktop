<template>
    <div>
        <Modal v-model="projectCreationModal" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-brush-outline" />
                <span>Create a project</span>
            </p>
            <div style="text-align:center">
                <Input type="text" v-model="projectName" placeholder="Project name">
                </Input> <br><br>
                <Input type="number" v-model="projectBudget" placeholder="Project budget">
                </Input>
            </div>
            <div slot="footer">
                <Button @click="createProject()" type="success" size="large">Create</Button>
            </div>
        </Modal>
    </div>
</template>

<script>

    import axios from 'axios';
    var API = "http://localhost:1337/";

    export default {
        name: 'ProjectCreationModal',
        data() {
            return {
                projectCreationModal: false,
                projectName: '',
                projectBudget: ''
            }
        },
        props: {
            user: Object
        },
        methods: {
            createProject() {
                var projectName = this.projectName;

                var authentication = {
                    username: localStorage.getItem('username'),
                    password: localStorage.getItem('password')
                }

                if(projectName != '' && projectName.length > 1 && this.projectBudget != '')
                {
                    this.$Loading.start();

                    this.projectCreationModal = false;

                    axios(API + "createProject", {
                        method: 'post',
                        data: { text: this.projectName, budget: this.projectBudget },
                        auth: authentication
                    }).then(response => {
                        this.$Loading.finish();
                        this.showMessage('Created!', 1.5);
                        this.user.projects.push(projectName);
                    }).catch(err => {
                        this.$Loading.error();
                        this.showError('Error!', 1.5);
                    });
                }
                else
                {
                    this.$Loading.error();
                    this.showError("Project name can't be empty or less than 2 characters! By the way all fields are required!", 3);
                }
            },
            toggle()
            {
                this.projectCreationModal = true;
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