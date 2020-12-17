<template>
  <v-container class="container" fluid>
    <v-row>
    <v-app-bar class="appbar">Github Visualiser | Michael Quigley</v-app-bar>
    </v-row>
    <v-row>
    <v-col class ="column">
    <v-row>
    <v-text-field
      v-model="inputtedUser"
      label="Search User..."
      solo
    ></v-text-field>
    <v-btn @click.native="retrieveData()"><v-icon>mdi-search</v-icon>Search</v-btn>
    </v-row>
      </v-col>
      <v-col>
        {{ this.userData }}
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios"
  export default {
    name: 'HelloWorld',
    data () {
      return {
        inputtedUser: "qu1gs",
        userData: null
      }
    },
   methods: {
     retrieveData () {
     let baseURL = "https://api.github.com"
     let username = this.inputtedUser
     var url = baseURL + "/users/" + username
     axios.get(url,{
       headers: {
         authorization: "token "+ process.env.VUE_APP_API_KEY
       },
       timeout: 1000
     })
     .then(response => {
       this.userData = response
       console.log(this.userData)
     })
     }
  }
}
</script>
<style scoped>
.container{
  margin:0;
  padding:0;
}
.appbar{
  content: center;
}
.column{
  margin-left:2%;
  margin-top:1%;
}
</style>
