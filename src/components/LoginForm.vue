<template>
  <form class="form" @submit.prevent="login">
    <h1>Vue Todos</h1>
    <div class="form-group">
      <label for="email">Login name</label>
      <div class="errors" v-if="errorMessage">
        {{ errorMessage }}
      </div>
      <input
        id="email"
        type="email"
        autocomplete="username"
        placeholder="email"
        required
        v-model="username"
      >
    </div>
    <div class="form-group">
      <label for="password">Password</label>
      <input
        type="password"
        id="password"
        placeholder="email"
        v-model="password"
        autocomplete="current-password"
        required
      >
    </div>
    <button type="submit">{{ isWorking ? 'Working ...' : 'LOGIN'}}</button>
  </form>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

export default {
  name: 'LoginForm',
  data: () => ({
    username: '',
    password: '',
    errorMessage: '',
    isWorking: false
  }),
  methods: {
    login () {
      // validation
      this.errorMessage = ''
      this.isWorking = true
      axios.post(
        'https://vue-todos.robertmckenney.ca/oauth/token',
        {
          grant_type: 'password',
          client_id: 2,
          client_secret: 'TJJiU0ULORMxmpToXaTX4J4qo1cttVJ3nGSkg1xk',
          username: this.username,
          password: this.password,
          scope: '*'
        }
      ).then(response => {
        const apiTokens = {
          expires_at: moment().add(response.data.expires_in, 'second').format(),
          ...response.data
        }
        this.$emit('userDidAuthenticate', apiTokens)
        this.username = ''
        this.password = ''
      }).catch(error => { this.handleError(error) })
        .finally(() => { this.isWorking = false })
      // redirect to the main application view
    },
    handleError (error) {
      if (error.response) {
        switch (error.response.status) {
          case 400: {
            this.errorMessage = 'Both username and password are required.'
            break
          }
          case 401: {
            this.errorMessage = 'Incorrect username or password.'
            break
          }
          default: {
            this.errorMessage = 'Sorry. There was an problem on the server.'
          }
        }
      } else if (error.request) {
        console.log(error.request)
      } else {
        console.log('Error: ', error.message)
      }
      console.log(error.config)
    }
  }
}
</script>

<style lang='scss'>


/*Fonts*/
@import url(//fonts.googleapis.com/css?family=Vidaloka);
$normalFont:  'Courier New', sans-serif;
$fancyFont:  'Vidaloka', serif;

/*Colors*/
$white: #fce6f1;
$pink: #f6afce;
$dark: #12000c;




.form {
  padding: 1.5em;
  font-family: $normalFont;
}
.form h1{
  text-align: center;
  font-family: $fancyFont;
  letter-spacing: 3px;
  margin-top:4%;
}
.form-group {
  margin: 1em 0;
  display: flex;
  flex-direction: column;
}
.form-group label {
  margin-bottom: 0.25em;
  font-size: 0.875em;
  color: $white;
  margin-bottom:3%;
  font-size:1.1rem;
  letter-spacing: 2px;
}
.form-group input {
  border-radius: 0.25rem;
  border: 1px solid $white;
  padding: 0.25em;
  font-size: 1.1em;
}
.form-group input::placeholder {
  opacity: 0.54;
}
.form button {
  background-color: $pink;
  border-radius: 0.25rem;
  box-shadow: rgba(0, 0, 0, 0.12) 0px 1px 4px 1px;
  color: $dark;
  font-family:$fancyFont;
  font-size: 1.1em;
  font-weight: 500;
  padding: 0.5em 0.75em;
}
.errors {
  border-radius: 0.25em;
  border: 1px solid hsl(348, 83%, 93%);
  color: hsl(348, 83%, 47%);
  background-color: hsl(348, 63%, 97%);
  padding: 1.5em;
}
</style>
