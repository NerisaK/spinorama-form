<template>
  <div>
    <show-users />
    <add-user />
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
  }
}
</script>

<style>
  html {
    font-family: Arial, Helvetica, sans-serif;
  }
</style>
