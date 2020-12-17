<template>
  <v-container>
    <v-text-field
      v-model="inputtedUser"
      label="Search User..."
      solo
    ></v-text-field>
  </v-container>
</template>

<script>
import axios from "axios"
  export default {
    name: 'HelloWorld',
    data () {
      return {
        authKey: "",
        inputtedUser: "qu1gs",
        userData: null
      }
    },
   methods: {
     retrieveData () {
     let baseURL = "https://api.github.com"
     let username = this.inputtedUser
     var url = baseURL + "/users" + username
     axios.get(url,{
       headers: {
         authorization: "token "+ this.token
       },
       timeout: 1000
     })
     .then(response => {
       this.userData = response
     })
     }
  }
}
</script>
