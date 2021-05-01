<template>
    <div>
        <div v-for="user in users" :key="user.id">
            <div class="user">
                <p class="name">{{user.name}}</p>
                <img v-if="user.avatar" :src="user.avatar" alt="avatar">
                <p v-if="user.emails">Emaily:</p>
                <ul>
                    <li v-for="(email, index) in user.emails" :key="index">
                        {{ email }}
                    </li>
                </ul>
            </div>
            
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return {
            users: [],
            api: "https://unsure-dandelion.herokuapp.com/api/people"
        }
    },
    created(){
        this.getUsers()
    },
    methods: {
        getUsers(){
            fetch(this.api)
            .then(res => res.json())
            .then(data => this.users = data.data.slice())
        }
    }
}
</script>

<style scoped>
    img {
        width: 100px;
        height: 100px;
    }
</style>