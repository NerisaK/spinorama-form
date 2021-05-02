<template>
  <div>
    <div class="container">
      <div class="header">
        <h1 class="logo">Spinorama</h1>
      </div>
      <div v-if="showUsers || showAddUser" class="picture">
          <img
            src="./assets/hockey-icon.svg"
            alt="hokejky"
          >
      </div>
      <show-users
        v-if="showUsers"
        @showForm="showAddUserForm"
        @changeUser="changeUser($event)"
        @userDeleted="getUsers"
        :users="users"
        :api="api"
      />
      <add-user
        v-if="showAddUser"
        @hideForm="hideForm"
        :api="api"
        :headers="headers"
      />
      <change-user
        v-if="showChangeUser"
        @hideForm="hideForm"
        :userID="userToEditID"
        :api="api"
        :headers="headers"
      />
    </div>
  </div>
</template>

<script>
import AddUser from './components/forms/AddUserForm'
import ChangeUser from './components/forms/EditUserForm'
import ShowUsers from './components/users/ShowAllUsers'

export default {
  name: 'App',
  components: {
    AddUser,
    ShowUsers,
    ChangeUser
  },
  data(){
    return {
      showUsers: true,
      showAddUser: false,
      showChangeUser: false,
      users: [],
      userToEditID: null,
      api: "https://unsure-dandelion.herokuapp.com/api/people",
      headers: {
        "Content-Type": "application/json",
        "Accept": "application/json"
      },
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
    showAddUserForm(){
      this.showAddUser = true;
      this.showUsers = false;
      this.showChangeUser = false;
    },
    hideForm(){
      this.showAddUser = false;
      this.showChangeUser = false;
      this.showUsers = true;
      this.getUsers();
    },
    changeUser(id){
      this.showUsers = false;
      this.showAddUser = false;
      this.showChangeUser = true;
      this.userToEditID = id;
    },    
  }
}
</script>

<style>
  html {
    font-family: Arial, Helvetica, sans-serif;
    word-wrap: break-word;
  }

  .container {
    align-content: center;
    margin: 50px auto;
    border: 1px solid hsla(0, 0%, 50%, 0.400);
    max-width: fit-content;
    color: hsl(200, 100%, 50%);
  }

  .header {
    padding: 4em 4em;
    background: linear-gradient(to bottom right,
      hsl(190, 80%, 60%) 50%, transparent 50.5%)
      no-repeat bottom,
      linear-gradient(0deg, hsl(190, 80%, 60%), hsl(190, 80%, 60%))
      no-repeat top;
    background-size: 100% 7em, 100% calc(100% - 7em)
  }

  .logo {
    color: hsl(210, 70%, 30%);
    font-size: 3.3rem;
    font-weight: 800;
    margin: auto auto 2.5em auto;
    text-align: center;
  }

  .picture {
    position: relative;
    bottom: 120px;
    float: center;
    margin: 2em auto -7em auto;
    width: 100px;
    height: 100px;
  }

  .picture img {
    background-color: white;
    border-radius: 50%;
    width: 100px;
    height: 100px;
  }

  .error {
    color: hsl(0, 80%, 60%);
    font-weight: 200;
    margin-bottom: 30px;    
  }

  .success{
    color: hsl(103, 50%, 60%);
    font-weight: 200;
    margin-bottom: 35px;
  }

  @media(max-width: 470px){
    .logo {
      font-size: 2.2rem;    
    }
    .header {
      padding: 4em 0;
    }

    .users {
      margin: 0.5em 3em;
    }

    ul{
      padding-left: 10px;
    }
  }
</style>
