<template>
  <div id="app">

    <div v-if="isLoggedIn"> 
      
    <button class="logoutBtn" type="button" @click="logoutUser">Logout</button>

    <the-header 
      v-once 
      :priorities="priorities"
      :categories="categories"
      @filter="filter"
    />
    <new-task-form 
      v-if="newTaskShown" 
      :priorities="priorities"
      :categories="categories"
      @addTask="addTask" 
    />

    <task-list v-if="active"
      :tasks="activeTasks"
      :class="activeTasks"
      :priorities="priorities"
      :categories="categories"
      :reversed="reversed"
      :filteredTasks="filteredTasks"
      :filterOn="filtered"
      name="active"
      key="active"
      @updateTask="updateTask"
      @toggleDone="toggleDone"
      @deleteTask="deleteTask"
    />
    <task-list v-if="!active" 
      :tasks="completedTasks"
      :class="{ 'completed': !!completedTasks.length }"
      :priorities="priorities"
      :categories="categories"
      :reversed="reversed"
      :filteredTasks="filteredTasks"
      :filterOn="filtered"
      name="completed"
      key="completed"
      @updateTask="updateTask"
      @toggleDone="toggleDone"
      @deleteTask="deleteTask"
    />
    </div>

    <login-form v-else @userDidAuthenticate="loginUser" />

  </div>

</template>

<script>
import NewTaskForm from '@/components/NewTaskForm'
import TaskList from '@/components/TaskList'
import TheHeader from '@/components/TheHeader'
import axios from 'axios'
import moment from 'moment'
import LoginForm from '@/components/LoginForm'

export default {
  name: 'app',
  components: { NewTaskForm, TaskList, TheHeader, LoginForm },
  data: () => ({
    newTaskShown:  false,
    newTask: {},
    users: {},
    active:true,
    reversed:false,
    filtered:false,
    tasks: [],
    filteredTasks: [],
    categories: [
      {name: "Home"},
      {name: "Work"},
      {name: "School"}
    ],
     priorities: [
      {name: "Low"},
      {name: "Medium"},
      {name: "High"}
     ],
    api: {
    accessToken: '',
    expiresAt: ''
  }
  }),
  computed: {
    activeTasks() {
      return this.tasks.filter(task => !task.isComplete)
    },
    completedTasks() {
      return this.tasks.filter(task => task.isComplete)
    },
    overdueTasks () {
      return this.activeTasks.filter(task => new Date(task.dueAt) < Date.now() && !task.isComplete)
    },
    upcomingTasks () {
      return this.activeTasks.filter(task => new Date(task.dueAt) >= Date.now())
    },
     isLoggedIn () {
      return this.api.accessToken && moment(this.api.expiresAt).isAfter()
    },
    axiosOptions () {
      return {
        baseURL: 'https://vue-todos.robertmckenney.ca/api',
        headers: { 'Authorization': `Bearer ${this.api.accessToken}` }
      }
    }
  },
  watch: {
    tasks: {
      handler: function () { this.syncTasks() },
      deep: true
    }
  },
  created () {
    this.resetForm()
    this.loadCachedData()
    this.tasks = JSON.parse(localStorage.getItem('taskList')) || []  
  },
  methods: {
    toggleDone(task) {
      task.isComplete = !task.isComplete
    },

    deleteTask (task) {
      const index = this.tasks.findIndex(t => t.id === task.id)
      this.tasks.splice(index, 1)
    },
    updateTask (task) {
      let target = this.tasks.find(t => t.id === task.id) // eslint-disable-line no-unused-vars
      target = Object.assign(target, task)
    },
    syncTasks () {
      localStorage.setItem('taskList', JSON.stringify(this.tasks))
    },
    openNewTaskForm() {
      this.newTaskShown = true
    },
    closeNewTaskForm() {
      this.newTaskShown = !this.newTaskShown 
    },
    toggleActive() {
      this.active = !this.active 
    },
    reverseTasks(){
      this.reversed = !this.reversed
    },
    filter(priority, category) {

      if (category === "")
      {
         this.filteredTasks = this.tasks.filter(task => {return task.priority === priority})
      }
      else if(priority === "")
      {
         this.filteredTasks = this.tasks.filter(task => {return task.category === category})
      }
      else{
       this.filteredTasks = this.tasks.filter(task => {return (task.priority === priority) && (task.category === category)})
      }

      if(category === "" && priority === ""){
        this.filtered = false
        this.filteredTasks = []
      }else{
        this.filtered = true
      }

    },
     resetForm () {
      this.newTask = {
        title: '',
        description: '',
        dueAt: moment().format(),
        priority: 2,
        category: null,
        isComplete: false
      }
    },
    loadCachedData () {
      
      let apiTokens = localStorage.getItem('todoApiTokens')
     
      if (apiTokens === undefined) return
      apiTokens = JSON.parse(apiTokens)
      this.loginUser(apiTokens)
    },
    loginUser (apiTokens) {
      this.api.accessToken = apiTokens.access_token
      this.api.expiresAt = apiTokens.expires_at
      this.saveApiTokens(apiTokens)
      this.refreshTasks()
    },

    saveApiTokens (apiTokens) {
      localStorage.setItem('todoApiTokens', JSON.stringify(apiTokens))
    },
    logoutUser () {
   
      this.api.accessToken = ''
      this.api.expiresAt = ''
      
      localStorage.removeItem('todoApiTokens')
    },
    refreshTasks () {
      axios.get('/tasks', this.axiosOptions)
     
        .then(({data: {data}}) => {

          this.taskList = Object.assign({}, ...data.map(t => ({ [t.id]: t })))
        })
        .catch(error => this.handleAPIErrors(error))
    },

    addTask (task) {
      axios.post('/tasks', task, this.axiosOptions)
     
        .then(({data: {data: t}}) => {
 
          this.$set(this.taskList, t.id, t)
          this.resetForm()
        })
        .catch(error => this.handleAPIErrors(error))
    },

    removeTask (task) {
      axios.delete(`/todos/${task.id}`, this.axiosOptions)
        .then(response => {

          this.$delete(this.taskList, task.id)
        })
        .catch(error => this.handleAPIErrors(error))
    },
     fetchUsers () {
      axios
        .get(`${this.baseUrl}/users`)
        .then(response => {

          this.users = Object.assign({}, ...response.data.map(user => ({[user.id]: user})))
        })
        .catch(error => this.handleAPIErrors(error))
    },

    getUser (id) {
      axios
        .get(`${this.baseUrl}/users/${id}`)
        .then(response => console.log(response.data))
        .catch(error => this.handleAPIErrors(error))
    },

    // Create a new object
    createUser (newUser) {

      axios
        .post(`${this.baseUrl}/users`, newUser)
     
        .then(({ data }) => {
    
          this.users[data.id] = data
          this.newUser = { name: '' }
        })
        .catch(error => this.handleAPIErrors(error))
    },


    updateUser (user) {
      axios
        .patch(`${this.baseUrl}/users/${user.id}`, user)
        .then(response => {

          this.users[user.id] = response.data
        })
        .catch(error => this.handleAPIErrors(error))
    },


    destroyUser (user) {
      axios
        .delete(`${this.baseUrl}/users/${user.id}`)
        .then(response => {
     
          delete this.users[user.id]
        })
        .catch(error => this.handleAPIErrors(error))
    },

   
    handleAPIErrors (error) {

      console.log(error.message)
    }
  },


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

#app {
  display: grid;
  justify-content: center;
  grid-auto-columns: 1fr 1fr 1fr 1fr 1fr;
  min-height:667px;
  font-size: 14pt;
  font-family: $normalFont;
  color: $white;
  background: radial-gradient(ellipse at bottom, $pink, $dark);
}
.the-header{
  grid-column: 1/5;
  text-shadow: $dark 2px 2px 3px;
}
.task-list{
  grid-column:1/5;
  background-color:$dark;
}
.new-task-form{
  grid-column:1/5;
}
.logoutBtn{
    width:20%;
    padding:1%;
    margin:4%;
    margin-bottom:0;
    background-color:lighten($pink,6%);
    border: none;
    box-shadow: $dark 1px 1px 1px 1px;
    color:$dark;
    letter-spacing: 2px;   
    font-size:.9rem;
    font-family: $fancyFont;
    &:active{
      box-shadow: none;
      background-color:$pink;
    }
}

</style>
