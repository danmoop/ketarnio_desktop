<template>
    <div>
        <Modal v-model="isModalActive" width="720">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="md-mail" />
                <span>Your inbox</span>
            </p>

            <div slot="header" style="text-align: center;">
                <br>
                <Button @click="deleteAll()"><h3><Icon type="md-trash" /> Delete all</h3></Button>
            </div>

            <div style="text-align:center">
                <ul>
                    <li style="list-style: none; font-size: 18px; margin-top: 10px;" v-for="message in user.messages.slice().reverse()">
                        <Card style="border: 1px solid #999;">
                            <h2 slot="title"><Icon type="md-person" /> {{ message.author }}</h2>
                            <p class="fz18">{{ message.content }}</p>
                            <Divider />
                            <p class="fz18">Message sent on {{ message.timeStamp }}</p>
                        </Card>
                    </li>
                </ul>

                <h2 v-if="user.messages.length == 0">No messages yet</h2>
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
        name: 'UserInbox',
        props: {
            user: Object
        },
        data() {
            return {
                isModalActive: false
            }
        },
        methods: {
            toggle() {
                this.isModalActive = true;
            },
            deleteAll() {
                // here we check if user has any messages, if not then there is nothing to delete
                // just show a message
                if(this.user.messages.length != 0) 
                {

                    var authentication = {
                        username: localStorage.getItem('username'),
                        password: localStorage.getItem('password')
                    }

                    axios(API + "clearUserInbox", {
                        method: 'get',
                        auth: authentication
                    })
                    .then(response => {
                        this.$parent.setUser(response.data);
                        this.$Message.success('Done!');
                    })
                    .catch(err => this.$Message.error(err));
                }
                else
                    this.$Message.success('Done!');
            }
        }
    }
</script>