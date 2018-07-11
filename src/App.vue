<template>
  <div id="app">
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
</template>

<script>
import NewTaskForm from '@/components/NewTaskForm'
import TaskList from '@/components/TaskList'
import TheHeader from '@/components/TheHeader'

export default {
  name: 'app',
  components: { NewTaskForm, TaskList, TheHeader },
  data: () => ({
    newTaskShown:  false,
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
     ]
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
    reversedTasks() {
      this.reversed =  !this.reversed
    }
  },
  watch: {
    tasks: {
      handler: function () { this.syncTasks() },
      deep: true
    }
  },
  created () {
    this.tasks = JSON.parse(localStorage.getItem('taskList')) || []
  },
  methods: {
    toggleDone(task) {
      task.isComplete = !task.isComplete
    },
    addTask (task) {
      this.tasks.push(task)
      this.closeNewTaskForm()
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





</style>
