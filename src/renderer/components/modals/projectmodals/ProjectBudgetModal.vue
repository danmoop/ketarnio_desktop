<template>
        <Modal v-model="isModalActive" width="720">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-brush-outline" />
                <span>Set a new project budget</span>
            </p>
            <div style="text-align:center">
                <Input size="large" type="number" v-model="budget" placeholder="New budget">
                </Input> <br><br>
                <Input size="large" type="text" v-model="reasonOfChange" placeholder="Explain change of budget">
                </Input>
            </div>
            <div slot="footer">
                <Button @click="setBudget()" type="success" size="large">Save</Button>
            </div>
        </Modal>
</template>

<script>

import axios from 'axios';
var API = "http://localhost:1337/";

export default {
    name: 'ProjectBudgetModal',
    props: {
        project: Object
    },
    data() {
        return {
            isModalActive: false,
            budget: '',
            reasonOfChange: ''
        }
    },
    methods:
    {
        toggle()
        {
            this.isModalActive = true;
        },
        setBudget()
        {
            var authentication = {
                username: localStorage.getItem('username'),
                password: localStorage.getItem('password')
            }

            axios(API + "setProjectBudget", {
                method: 'post',
                data: {
                    projectName: this.project.projectName,
                    budget: this.budget,
                    reasonOfChange: this.reasonOfChange
                },
                auth: authentication
            }).then(response => {
                this.$parent.setProject(response.data);
                this.showMessage('Done!', 1.5);
            }).catch(err => this.showError(err, 3));

            this.isModalActive = false;
            
            // Empty textfields whether it is success response or not
            this.budget = '';
            this.reasonOfChange = '';
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