<template>
  <v-container class="container" fluid>
    <v-row>
      <v-app-bar><v-spacer/>Github Visualiser | Michael Quigley<v-spacer/></v-app-bar>
    </v-row>
    <v-row>
    <v-col class="column">
    <v-card class="searchCard mx-auto" align="center" justify="center" max-width="900px">
    <v-row>
    <v-text-field 
      height="50px"
      v-model="inputtedUser"
      label="Search User..."
      solo
    ></v-text-field>
    <v-btn height="50px" @click.native="submit()"><v-icon>mdi-search</v-icon>Search</v-btn>
    <v-card v-if="repoData">{{ this.commitData }}</v-card>
    </v-row>
    </v-card>
    {{ this.repoCommits }}
    {{ this.languages }}
      </v-col>
      <v-col v-if="login">
        <v-card max-width="900px">
        <v-row>
          <v-col class="justify-center">
        <v-img :src=avatar max-width="200px"></v-img>
          </v-col>
          <v-col>
        <v-card-title>User Information</v-card-title>
          <v-card-subtitle>
            <br>Login: {{ login }}
            <br>Full Name: {{ fullName }}
            <br>Company: {{ company }}
         </v-card-subtitle>
         </v-col>
        </v-row>
        </v-card>
        <v-card max-width="900px">
      <v-simple-table>
        <tbody>
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
      </v-card>
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
        baseURL: "https://api.github.com",
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
        listOfRepos: null,
        repoCommits: [],
        languages: []
      }
    },
   methods: {
     submit(){
      this.commitNames = null
      this.commitValues = []
      this.retrieveData()
      this.retrieveListOfRepos()
     },
     retrieveData () {
     var urlUserData = this.baseURL + "/users/" + this.inputtedUser
     axios.get(urlUserData,{
       headers: {
         authorization: "token "+ process.env.VUE_APP_API_KEY
       },
       timeout: 10000
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
    retrieveListOfRepos (){
      var urlCommitData = this.baseURL + "/users/" + this.inputtedUser +"/repos"
      return axios.get(urlCommitData,{
      headers: {
        authorization: "token "+ process.env.VUE_APP_API_KEY
      },
      timeout: 10000
      })
      .then(response => {
        this.listOfRepos = response.data
        setTimeout(this.retrieveLanguages(), 2000)
        setTimeout(this.retrieveCommitData(), 2000)
      })
    },
    retrieveCommitData () {
        var commits = new Map()
        for (var i = 0; i < this.listOfRepos.length; i++){
        let repoName = this.listOfRepos[i].name
        axios.get(this.baseURL + "/repos/" + this.login + "/" + this.listOfRepos[i].name + "/stats/contributors" ,{
        headers: {
          authorization: "token "+ process.env.VUE_APP_API_KEY
        },
        timeout: 10000         
        })
        .then(response => {
          let commitStats = response.data;
          for (var j = 0; j < commitStats.length; j++){
            if (commitStats[j] !== undefined && commitStats[j].author.login === this.login) {
             commits.set(repoName, commitStats[j].total);
            }
          }
          this.repoCommits = [];
          for (const [key, value] of commits){
            if (key !== "undefined" && key !== undefined) {
              this.repoCommits.push([key, value])
            }
          }
        })            
        }
      },
      retrieveLanguages () {
        var languages = new Map();
        for (var i = 0; i < this.listOfRepos.length; i++){
        axios.get(this.baseURL + "/repos/" + this.login + "/" + this.listOfRepos[i].name + "/languages", {
        headers: {
          authorization: "token "+ process.env.VUE_APP_API_KEY
        },
        timeout: 10000          
        }).then(response => {
          for(const [key, value] of Object.entries(response.data)){
            if(key !== undefined && key !== "undefined") {
              if(languages.has(key)){
                let num = languages.get(key);
                languages.set(key, num + value)
                } else {
                  languages.set(key, value)
                }
              }
            }
          this.languages  = [];
          for(const [key, value] of languages){
            if(key !== undefined && key !== "undefined") {
              this.languages.push([key,value])
            }
          }
      })
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
