<template>
  <div class="about">
    <h1 class="text-danger">Hi There Aphrodite</h1>
    <button v-on:click.prevent="save">Cadastrar</button>
    |
    <button v-on:click.prevent="signIn">Login</button>
  </div>
</template>

<style>
  .text-danger {
    color: red;
  }
</style>


<script>
import {
  CognitoUserPool,
  CognitoUserAttribute,
  CognitoUser,
  AuthenticationDetails,
} from "amazon-cognito-identity-js";

const VUE_APP_COGNITO_USER_POOL_ID = process.env.VUE_APP_COGNITO_USER_POOL_ID;
const VUE_APP_COGNITO_CLIENT_ID = process.env.VUE_APP_COGNITO_CLIENT_ID;
export default {
  name: 'home',
  data () {
      return {
          sendingMessage: false,
          login: {
            username: "diegolirio",
            email: "diegolirio.dl@gmail.com",
            password: "F684rt6eg5hrteh@23546",
          },          
      }
  },
  methods: {
    // ...mapActions({
    //   signUp: "loginStore/signUp",
    // }),
    save() {
      this.signUp(this.login);
    },
    signUp(login) {
      var poolData = {
        UserPoolId: VUE_APP_COGNITO_USER_POOL_ID,
        ClientId: VUE_APP_COGNITO_CLIENT_ID
      };
      var userPool = new CognitoUserPool(poolData);
      var attributeList = [];
      var attributeEmail = new CognitoUserAttribute({
        Name: "email",
        Value: login.email,
      });
      attributeList.push(attributeEmail);
      userPool.signUp(
        login.username,
        login.password,
        attributeList,
        null,
        (err, result) => {
          if (err) {
            console.log(err);
          }
          var cognitoUser = result.user;
          console.log(cognitoUser);
        }
      );
    },    
    signIn() {
         var authenticationData = {
           Username: this.login.username,
           Password: this.login.password,
         };
         var authenticationDetails = new AuthenticationDetails(
           authenticationData
         );
         var poolData = {
           UserPoolId: VUE_APP_COGNITO_USER_POOL_ID,
           ClientId: VUE_APP_COGNITO_CLIENT_ID,
         };

         var userPool = new CognitoUserPool(poolData);
         var userData = {
           Username: this.login.username,
           Pool: userPool,
         };
         var cognitoUser = new CognitoUser(userData);
        
         cognitoUser.authenticateUser(authenticationDetails, {
           onSuccess: function (result) {
             localStorage.setItem('access_token', result.getAccessToken().getJwtToken());
             localStorage.setItem('id_token', result.getIdToken().getJwtToken());
             //commit('setLoggedUserUser', result.getIdToken().payload);
           },
            onFailure: function (err) {
              console.log(err);
            },           
         });
    }
  }
}
</script>
