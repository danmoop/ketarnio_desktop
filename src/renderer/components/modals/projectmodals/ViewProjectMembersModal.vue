<template>
    <div>
        <Modal v-model="isModalActive" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-people-outline" />
                <span>{{ project.projectName}} members ({{ project.members.length }})</span>
            </p>
            <div style="text-align:center">
                <ul>
                    <li style="list-style: none; font-size: 18px;" v-for="member in project.members">
                        {{project.members.indexOf(member) + 1}}. {{ member }}
                    </li>
                </ul>
            </div>
            <div slot="footer">
                <Button @click="() => this.isModalActive = false" type="success" size="large">OK</Button>
            </div>
        </Modal>
    </div>
</template>

<script>
export default {
    name: 'ViewProjectMembersModal',
    props: {
        project: Object
    },
    data() {
        return {
            isModalActive: false
        }
    },
    methods: {
        toggle() {
            this.isModalActive = true;
        }
    }
}
</script>