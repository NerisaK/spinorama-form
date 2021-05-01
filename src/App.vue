<template>
  <div>
    <div class="container">
      <div class="header">
        <h1 class="logo">Spinorama</h1>
      </div>
      <keep-alive>
        <show-users v-if="showUsers" @showForm="showForm"/>
      </keep-alive>
      <add-user v-if="showAddUser" @hideForm="hideForm"/>
    </div>
  </div>
</template>

<script>
import AddUser from './components/forms/AddUserForm'
import ShowUsers from './components/users/ShowAllUsers'

export default {
  name: 'App',
  components: {
    AddUser,
    ShowUsers
  },
  data(){
    return {
      showUsers: true,
      showAddUser: false
    }
  },
  methods: {
    showForm(){
      this.showAddUser = true;
      this.showUsers = false;
    },
    hideForm(){
      this.showAddUser = false;
      this.showUsers = true;
    }
  }
}
</script>

<style>
  html {
    font-family: Arial, Helvetica, sans-serif;
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
      hsl(190, 80%, 50%) 50%, transparent 50.5%)
      no-repeat bottom,
      linear-gradient(0deg, hsl(190, 80%, 50%), hsl(190, 80%, 50%))
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

  @media(max-width: 470px){
    .logo {
      font-size: 2.2rem;    
    }
    .header {
      padding: 4em 0;
    }
  }
</style>
