<template>
    <div>
        <Modal v-model="isModalActive" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-brush-outline" />
                <span>Set / delete a notification for all the members</span>
            </p>
            <div style="text-align:center">
                <Input type="text" v-model="notificationText" placeholder="Notification text">
                </Input>
            </div>
            <div slot="footer">
                <Button @click="deleteNotification()" type="error" size="large">Delete</Button>
                <Button @click="createNotification()" type="success" size="large">Save</Button>
            </div>
        </Modal>
    </div>
</template>

<script>

import axios from 'axios';
var API = "http://localhost:1337/";

export default {
    name: 'ProjectNotificationModal',
    props: {
        project: Object
    },
    data() {
        return {
            isModalActive: false,
            notificationText: ''
        }
    },
    methods: {
        createNotification()
        {
            this.isModalActive = false;

            if(this.notificationText.length > 0)
            {
                var authentication = {
                    username: this.username || localStorage.getItem('username'),
                    password: this.password || localStorage.getItem('password')
                }

                axios(API + 'setProjectNotification', {
                    method: 'post',
                    data: {
                        projectName: this.project.projectName,
                        text: this.notificationText,
                    },
                    auth: authentication
                }).then(response => this.$parent.refreshProject(true)).catch(err => alert(err));
            } else {
                this.showError("Notification can't be empty!", 2);
            }

            this.notificationText = '';
        },
        deleteNotification()
        {
            this.isModalActive = false;

            if(this.project.projectNotification != null) // why delete when it's already deleted, so here is null-check
            {
                var authentication = {                    
                    username: this.username || localStorage.getItem('username'),
                    password: this.password || localStorage.getItem('password')
                }

                axios(API + 'removeProjectNotification', {
                    method: 'post',
                    data: {
                        projectName: this.project.projectName,
                    },
                    auth: authentication
                }).then(response => this.$parent.refreshProject(true)).catch(err => alert(err));
            }
            else // if it is deleted, just notify user
                this.showMessage('Done!', 1.5);
        },
        toggle()
        {
            this.isModalActive = true;
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
