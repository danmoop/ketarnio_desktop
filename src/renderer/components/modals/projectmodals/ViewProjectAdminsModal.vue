<template>
    <div>
        <Modal v-model="isModalActive" width="360">
            <p slot="header" style="color:#42b983;text-align:center">
                <Icon type="ios-star-outline" />
                <span>{{ project.projectName}} admins ({{ project.admins.length }})</span>
            </p>
            <div style="text-align:center">
                <ul>
                    <li style="list-style: none; font-size: 18px;" v-for="admin in project.admins">
                        {{project.admins.indexOf(admin) + 1}}. {{ admin }}
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
    name: 'ViewProjectAdminsModal',
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