<template>
    <div class="users">
        <h2>Seznam uživatelů</h2>
        <div v-for="(user, index) in users" :key="user.id">
            <div class="user">
                <p class="name" @click.prevent="showDetail(index)">{{user.name}}</p>
                <div class="info" v-show="user.showDetail">
                   <img class="avatar" v-if="user.avatar" :src="user.avatar" alt="avatar">
                    <p v-if="user.emails">Emaily:</p>
                    <ul>
                        <li v-for="(email, index) in user.emails" :key="index">
                            {{ email }}
                        </li>
                    </ul>
                    <button class="change-btn">změnit údaje</button> 
                </div>
                
            </div>
            
        </div>
        <div class="add-div" @click.prevent="$emit('showForm')">
            <button class="add-btn"> Přidat uživatele </button>
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
        },
        showDetail(index){
            let user = this.users[index];
            
            if(user.showDetail === true){
                user.showDetail = false
            }
            else {
                user.showDetail = true;    
            }
        }
    }
}
</script>

<style scoped>

    .users {
        margin: 2em 4em;
        color: hsl(200, 100%, 45%);
        font-size: 0.9rem;        
    }

    .user {
        border-top: 2px solid hsla(200, 100%, 45%, 0.2);
        border-left: 2px solid hsla(200, 100%, 45%, 0.2);
        border-right: 3px solid hsla(200, 100%, 45%, 0.3);
        border-bottom: 3px solid hsla(200, 100%, 45%, 0.3);
        padding: 5px 10px;
        margin-bottom: 15px;
    }

    h2 {
        margin-bottom: 35px;
        color: hsl(205, 70%, 50%); 
        text-align: center;
    }

    .name {
        font-weight: bold;
        color: hsl(200, 100%, 70%);
        font-size: 1rem; 
    }

    .name:hover {
        cursor: pointer;
        color: hsl(200, 100%, 60%);
    }

    .info {
        margin-top: 20px;
        margin-left: 10px;
    }

    .avatar {
        width: 100px;
        height: 100px;
    }

    .add-div {
        display: flex;
        justify-content: center;
    }

    button {
        background-color: hsl(200, 100%, 60%);
        border-radius: 0.5rem;
        border: none;
        text-align: center;
        color: white;    
    }

    button:hover{
        cursor: pointer;
        background-color: hsl(200, 100%, 50%);
    }

    .add-btn {
        padding: 1em 3em;
        margin-top: 2em;
    }

    .change-btn {
        padding: 0.5em 1em;
        margin: 10px 0;
    }

</style>