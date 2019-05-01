<template>
    <div>
        <Modal v-model="isModalActive" width="720">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="md-mail" />
                <span>{{ project.projectName}} inbox ({{ project.messages.length }})</span>
            </p>

            <div slot="header" style="text-align: center;">
                <br>
                <Button @click="deleteAll()"><h3><Icon type="md-trash" /> Delete all</h3></Button>
            </div>

            <div style="text-align:center">
                <ul>
                    <li style="list-style: none; font-size: 18px; margin-top: 10px;" v-for="message in project.messages.slice().reverse()">
                        <Card style="border: 1px solid #999;">
                            <h2 slot="title"><Icon type="md-person" /> {{ message.author }}</h2>
                            <p class="fz18" style="white-space: pre;">{{ message.content }}</p>
                            <p class="fz18">Message sent on {{ message.timeStamp }}</p>
                        </Card>
                    </li>
                </ul>

                <h2 v-if="project.messages.length == 0">No messages yet</h2>
            </div>
            <div slot="footer">
                <Button @click="() => this.isModalActive = false" type="success" size="large">OK</Button>
            </div>
        </Modal>
    </div>    
</template>

<script>

    import axios from 'axios';
    var API = "http://localhost:1337/";

    export default {
        name: 'ProjectInboxModal',
        props: {
            project: Object
        },
        data() {
            return {
                isModalActive: false
            }
        },
        methods: {
            toggle()
            {
                this.isModalActive = true;
            },
            deleteAll()
            {
                axios(API + "deleteAllInboxMessages", {
                    method: 'post',
                    data: {
                        projectName: this.project.projectName
                    },
                    auth: {
                        username: this.username || localStorage.getItem('username'),
                        password: this.password || localStorage.getItem('password')
                    }
                })
                .then(response => {
                    this.$parent.setProject(response.data);
                    this.$Message.success('Done!');
                })
                .catch(err => this.$Message.error(err));
            }
        }
    }
</script>

<style>
    .fz18 {
        font-size: 18px;
    }
</style>
