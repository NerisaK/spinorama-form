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
                    <button class="change-btn"
                        @click.prevent="$emit('changeUser', user.id)">změnit údaje
                    </button>
                    <button class="delete-btn"
                        @click.prevent="deleteUser(user.id)">smazat uživatele
                    </button>  
                    <p class="error" v-if="deleteError[user.id]">Uživatele se nepodařilo vymazat</p>
                    <p class="success" v-if="deleteSuccess[user.id]">Uživatel byl úspěšně vymazán</p>
                </div>
                
            </div>
            
        </div>
        <div class="add-div" @click.prevent="$emit('showForm')">
            <button class="add-btn"> Přidat uživatele </button>
        </div>
    </div>
</template>

<script scoped>
export default {
    props: {
        users: Array,
        api: String,
        
    },
    data(){
        return {
            deleteError: [],
            deleteSuccess: []  
        }
    },
    methods: {
        showDetail(index){
            let user = this.users[index];
            
            if(user.showDetail === true){
                user.showDetail = false
            }
            else {
                user.showDetail = true;    
            }
        },
        deleteUser(id){
            fetch(this.api + "/" + id, {method: 'DELETE'})
            .then(response => {
              if(response.ok) {
                  console.log("User was successfully deleted")
                this.deleteSuccess[id] = true;
                setTimeout(()=>{
                    this.deleteSuccess[id] = false;
                    this.$emit('userDeleted')
                }, 2000)
              }
            })
            .catch(error => {
                console.log(error); 
                this.deleteError[id] = true;
                setTimeout(()=>{
                    this.deleteError[id] = false;
                }, 2000)
            })
        }
    }
}
</script>

<style scoped>
    @import '../../assets/users.css';
</style>