<template>
    <div>
        <Modal v-model="isModalActive" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-brush-outline" />
                <span>Change your notes</span>
            </p>
            <div style="text-align:center">
                <Input size="large" type="text" v-model="user.note" placeholder="Notes">
                </Input>
            </div>
            <div slot="footer">
                <Button @click="setNote()" type="success" size="large">Save</Button>
            </div>
        </Modal>
    </div>
</template>

<script>
    import axios from 'axios';
    var API = "http://localhost:1337/";

    export default {
        name: 'setNoteModal',
        props: {
            user: Object
        },
        data() {
            return {
                isModalActive: false
            }
        },
        methods: {
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
                    this.showMessage('Saved!', 1.5);
                    this.isModalActive = false;
                }).catch(err => console.log(err));
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
