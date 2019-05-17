<template>
    <Modal v-model="isModalActive" width="360">
        <p slot="header" style="color:#42b983;text-align:center">
            <Icon type="md-person" />
            <span>Invite a member</span>
        </p>
        <div style="text-align:center">
            <Input size="large" type="text" v-model="username" placeholder="Username">
            </Input>
        </div>
        <div slot="footer">
            <Button @click="invite()" type="success" size="large">Invite</Button>
        </div>
    </Modal>
</template>

<script>

    import axios from 'axios';
    var API = "http://localhost:1337/";

    export default {
        name: 'InviteMembersModal',
        props: {
            project: Object
        },
        data() {
            return {
                username: '',
                isModalActive: false
            }
        },
        methods: {
            toggle() {
                this.isModalActive = true;
            },  
            invite() {
                axios(API + `inviteUserToProject/${this.project.projectName}/${this.username}`, {
                    method: 'post',
                    auth: {
                        username: localStorage.getItem('username'),
                        password: localStorage.getItem('password')
                    }
                }).then(response => {
                    if(response.data == "Done!")
                    {
                        this.$Message.success(response.data);
                        this.isModalActive = false;
                    }
                    else   
                        this.$Message.error(response.data);
                }).catch(err => this.$Message.error(err));
            }
        }
    }
</script>