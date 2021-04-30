<template>
  <div>
    <add-user-form />
  </div>
</template>

<script>
import AddUserForm from './components/AddUserForm'

export default {
  name: 'App',
  components: {
    AddUserForm
  }
}
</script>

<style>
  html {
    font-family: Arial, Helvetica, sans-serif;
  }
</style>
