<template>
  <v-container class="container" fluid>
    <v-row>
      <v-app-bar><v-spacer/>Github Visualiser | Michael Quigley<v-spacer/></v-app-bar>
    </v-row>
    <v-row>
    <v-col class="column">
    <v-card class="searchCard mx-auto" align="center" justify="center">
    <v-row>
    <v-text-field height="50px"
      v-model="inputtedUser"
      label="Search User..."
      solo
    ></v-text-field>
    <v-btn height="50px" @click.native="submit()"><v-icon>mdi-search</v-icon>Search</v-btn>
    <v-card v-if="repoData">{{ this.commitData }}</v-card>
    </v-row>
    </v-card>
      </v-col>
      <v-col>
      <v-simple-table v-if="login">
        <tbody>
          <tr>
            <td>Login:</td>
            <td>{{ this.login }}</td>
          </tr>
          <tr>
            <td>Avatar:</td>
            <td>{{ this.avatar }}</td>
          </tr>
          <tr>
            <td>Full Name:</td>
            <td>{{ this.fullName }}</td>
          </tr>
          <tr>
            <td>Company:</td>
            <td>{{ this.company }}</td>
          </tr>
          <tr>
            <td>Looking for Work:</td>
            <td>{{ this.lookingForWork }}</td>
          </tr>
          <tr>
            <td>Email</td>
            <td>{{ this.email }}</td>
          </tr>
          <tr>
            <td>Number of Repos:</td>
            <td>{{ this.noOfRepos }}</td>
          </tr>
          <tr>
            <td>Looking for Work</td>
            <td>{{ this.lookingForWork }}</td>
          </tr>
          <tr>
            <td>Twitter</td>
            <td>{{ this.twitterUserName }}</td>
          </tr>
          <tr>
            <td>Number of Followers</td>
            <td>{{ this.noOfFollowers }}</td>
          </tr>
          <tr>
            <td>Number of Following</td>
            <td>{{ this.noOfFollowing }}</td>
          </tr>
          <tr>
            <td>Join Date</td>
            <td>{{ this.dateCreated }}</td>
          </tr>
        </tbody>
      </v-simple-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios"
  export default {
    name: 'Main',
    data () {
      return {
        inputtedUser: "qu1gs",
        userData: null,
        displayData: false,
        login: null,
        avatar: null,
        fullName: null,
        company: null,
        location: null,
        email: null,
        lookingForWork: null,
        twitterUserName: null,
        noOfRepos: null,
        noOfFollowers: null,
        noOfFollowing: null,
        dateCreated: null,
        repoData: null,
        commitData: []
      }
    },
   methods: {
     submit(){
      this.retrieveData()
      this.retrieveRepoData()
      this.retrieveAllData()
     },
     retrieveData () {
     let baseURL = "https://api.github.com"
     let username = this.inputtedUser
     var urlUserData = baseURL + "/users/" + username
     axios.get(urlUserData,{
       headers: {
         authorization: "token "+ process.env.VUE_APP_API_KEY
       },
       timeout: 1000
     })
     .then(response => {
       this.userData = response
       this.login = this.userData.data.login
       this.avatar = this.userData.data.avatar_url
       this.fullName = this.userData.data.name
       this.company = this.userData.data.company
       this.location = this.userData.data.location
       this.email = this.userData.data.email
       this.lookingForWork = this.userData.data.hireable
       this.twitterUserName = this.userData.data.twitter_username
       this.noOfRepos = this.userData.data.public_repos
       this.noOfFollowers = this.userData.data.followers
       this.noOfFollowing = this.userData.data.following
       this.dateCreated = this.userData.data.created_at
     })
     },
     retrieveRepoData () {
     let baseURL = "https://api.github.com"
     let username = this.inputtedUser
     var urlRepoData = baseURL + "/users/" + username +"/repos"
       axios.get(urlRepoData,{
       headers: {
         authorization: "token "+ process.env.VUE_APP_API_KEY
       },
       timeout: 1000
     })
     .then(response => {
       this.repoData = response
     })
     },
     retrieveCommitData (page) {
     let baseURL = "https://api.github.com"
     let username = this.inputtedUser
     var urlCommitData = baseURL + "/users/" + username +"/events?page=" + page 
     return axios.get(urlCommitData,{
       headers: {
         authorization: "token "+ process.env.VUE_APP_API_KEY
       },
       timeout: 1000
     })
     .then(response => {
       if(response.data.length != 0){
        this.commitData.push (response.data)
        console.log(this.commitData)
       }
     })
     },
      retrieveAllData() {
      this.commitData = []
      for(var i = 1; i <= 10; i++){
        this.retrieveCommitData(i)
      }
    }
   }
}
</script>
<style scoped>
.container{
  margin:0;
  padding:0;
}
.column{
  margin:1%;
}
.searchCard {
  padding:20px;
}
</style>
