<template>
  <form class="new-task-form" @submit.prevent="addTask">
    <h3> Create New Task </h3>
    <label>
    Title
    </label>
    <input v-model.trim="newTask.title" type="text" maxlength="25">
    <label>
    Due At
    </label>
    <input v-model="newTask.dueAt" type="date">
    <label>
    Description
    </label>
    <input v-model.trim="newTask.description" type="text" maxlength="100">
    <label>
    Category
    </label>
    <select v-model="newTask.category">
      <option v-for="category in categories" :key="category.name">
          {{category.name}}
      </option>
    </select>
    <label>
    Priority
    </label>
    <select v-model="newTask.priority">
      <option v-for="priority in priorities" :key="priority.name">
          {{priority.name}}
      </option>
    </select>
    
    <div class="buttonArea">
      <button type="submit">Add</button>
      <button type ="button" @click="closeNewTaskForm()">Cancel</button>
    </div>

  </form>
</template>

<script>

export default {
  props:['priorities','categories'], 
  data: () => ({
    newTask: {},
  }),
  created () {
    this.resetNewTask()
  },
  methods: {
    addTask () {
      this.$emit('addTask', this.newTask)
      this.resetNewTask()
    },
    resetNewTask () {
      this.newTask = {
        id: Date.now(),
        title: '',
        dueAt: (new Date()).toISOString().split('T')[0],
        description: '',
        category: '',
        priority: '',
        isComplete: false
      }
    },
    closeNewTaskForm() {
      this.$parent.closeNewTaskForm()
      this.resetNewTask()
    }
  }
}
</script>

<style lang="scss">

/*Fonts*/
@import url(//fonts.googleapis.com/css?family=Vidaloka);
$normalFont:  'Courier New', sans-serif;
$fancyFont:  'Vidaloka', serif;

/*Colors*/
$white: #fce6f1;
$pink: #f6afce;
$dark: #12000c;

.new-task-form {
  padding: 0 5% 2% 5%;
  display: grid;
  grid-auto-columns: 1fr 1fr 1fr 1fr;
  grid-auto-rows: auto 1fr;
  background-color:$white;

  h3{
    grid-column: 1/4;
    text-align: center;
    color:$dark;
    letter-spacing: 2px;
    margin:1rem;
  }

  label {
    grid-column: 1 / 4;
    display: flex;
    flex-direction: column;
    margin:1rem auto .7rem auto;
    font-family: $fancyFont;
    font-weight:bold;
    letter-spacing: 2px;
    color:$dark;
  }

  input , select {
    grid-column: 1 / 4;
    background: hsl(0, 0%, 97%);
    border: 1px solid hsl(0, 0%, 93%);
    font-size: 1.3rem;
    padding: 0.4em;
    margin:2%;
    border:2px solid $dark;
    border-radius: 0.25rem;
  }

  .buttonArea{
    grid-column: 1/4;
    text-align: center;
  }

  button {
    align-self: center;
    background-color: $dark;
    border: 1px solid $white;
    border-radius: 0.25rem;
    box-shadow: 0px 1px 3px 0px rgba(0, 0, 0, 0.25);
    width: 40%;
    padding: .5rem;
    margin:3%;
    color: hsl(0, 0%, 99%);
    font-family:$fancyFont;
    font-weight: normal;
    font-size: 1rem;
    letter-spacing: 4px;
    &:active {
      box-shadow: none;
      background-color:$pink;
      color:$dark;
    }
  }
  

  
}
</style>
