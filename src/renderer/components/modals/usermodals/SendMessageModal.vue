<template>
    <div>
        <Modal v-model="isModalActive" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="md-send" />
                <span>Send a message</span>
            </p>
            <div style="text-align:center">
                <Input size="large" type="text" v-model="recipient" placeholder="Recipient">
                </Input> <br> <br>
                <Input size="large" type="text" v-model="message" placeholder="Message">
                </Input>
            </div>
            <div slot="footer">
                <Button @click="sendMessage()" type="success" size="large">Send</Button>
            </div>
        </Modal>
    </div>
</template>

<script>
    import axios from 'axios';
    var API = "http://localhost:1337/";

    export default {
        name: 'SendMessageModal',
        data() {
            return {
                isModalActive: false,
                recipient: '',
                message: ''
            }
        },
        methods:
        {
            toggle()
            {
                this.isModalActive = true;
            },
            sendMessage() 
            {
                axios(API + "sendMessageToUser", {
                    method: 'post',
                    data: {
                        recipient: this.recipient,
                        message: this.message
                    },
                    auth: {
                        username: localStorage.getItem('username'),
                        password: localStorage.getItem('password')
                    }
                }).then(response => {
                    if(response.data == "Done!")
                    {
                        this.$Message.success(response.data);
                        this.isModalActive = false;

                        // message is sent, so empty textfields
                        this.recipient = '';
                        this.message = '';

                        this.$parent.refreshProfile(false);
                    }
                    else
                        this.$Message.error({content: response.data, duration: 2});
                }).catch(err => this.$Message.error(err));
            }
        }
    }
</script>