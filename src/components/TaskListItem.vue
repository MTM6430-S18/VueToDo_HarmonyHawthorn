<template>
    <div v-if="isEditing" class="task-list-item is-editing" @keyup.esc="cancel">
      <form  @submit.prevent="save">
        <h3> Edit </h3>
        <label>
          Title
        </label>
          <input v-model.trim="editTask.title" type="text" maxlength="25"/>

        <label>
          Due At
        </label>
          <input v-model="editTask.dueAt" type="date"/>

        <label>
          Description
        </label>
          <input v-model="editTask.description" type="text" maxlength="100"/>

         <label>
          Category
         </label>
          <select v-model="editTask.category">
          <option>
            {{task.category}}
          </option>
            <option v-for="category in categories" :key="category.name" v-if="category.name != task.category">
                {{category.name}}
            </option>
          </select>

        <label>
          Priority
         </label>
           <select v-model="editTask.priority">
            <option>
              {{task.priority}}
            </option>
            <option v-for="priority in priorities"  :key="priority.name" v-if="priority.name != task.priority">
                {{priority.name}}
            </option>
          </select>     

        <span class="buttonArea action-buttons">
          <button class="button" type="submit">Save</button>
          <button class="button isOutline" type="button" @click="cancel">Cancel</button>
        </span>

      </form>
    </div>

   <div v-else class="task-list-item not-editing">
      <font-awesome-icon :icon="statusIcon" @click="$emit('toggleDone', task)"/>
      <span>{{task.title}}</span>
      <span>{{task.dueAt}}</span>
      <span>{{task.description}}</span>
      <span>{{task.category}}</span>
      <span>{{task.priority}}</span>
      <font-awesome-icon :icon="editIcon" @click="edit"/>
      <font-awesome-icon :icon="deleteIcon" @click="$emit('deleteTask', task)"/>
    </div>
</template>

<script>
import FontAwesomeIcon from '@fortawesome/vue-fontawesome'
import faEdit from '@fortawesome/fontawesome-free-regular/faEdit'
import faCircle from '@fortawesome/fontawesome-free-regular/faCircle'
import faCheckCircle from '@fortawesome/fontawesome-free-regular/faCheckCircle'
import faTrashAlt from '@fortawesome/fontawesome-free-regular/faTrashAlt'

export default {
  components: { FontAwesomeIcon },
  directives: {
    focus: {
      inserted (el) {
        el.focus()
      }
    }
  },
  props: ['task', 'categories', 'priorities'],
  data: () => ({
    isEditing: false,
    editTask: {},
  }),
  computed: {
    statusIcon () {
      return this.task.isComplete ? faCheckCircle : faCircle
    },
    editIcon () {
      return faEdit
    },
    deleteIcon () {
      return faTrashAlt
    }
  },
  
  methods: {
    cancel () {
      this.isEditing = false
    },
    edit () {
      this.editTask = Object.assign({}, this.task)
      this.isEditing = true
    },
    save () {
      this.$emit('updateTask', this.editTask)
      this.isEditing = false
    }
  }
}
</script>

<style lang="scss">

/*Fonts*/
@import url(//fonts.googleapis.com/css?family=Vidaloka);
$normalFont:  'Courier New', sans-serif;
$fancyFont:   'Vidaloka', serif;

/*Colors*/
$white: #fce6f1;
$pink: #f6afce;
$dark: #12000c;


h3{
  text-align: center;
  font-family: $fancyFont;
  letter-spacing: 2px;
  margin:1rem;
}
.not-editing{
  background-color: $dark; 
  border-radius:1rem;
  margin: 2%;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  grid-auto-rows: 1fr auto 1fr;
  grid-gap: 1rem;
  align-items: center;
  justify-items:center;
  font-size:1rem;
  padding:3%;
  /*Checkbox*/
  :nth-child(1){
    font-size:1.4rem;
    justify-self: left;
    grid-row: 1 / 2;
    grid-column: 1 / 2;    
  }
  /*Title*/
  :nth-child(2){
    font-family:$fancyFont;
    font-size:1.1rem;
    justify-self: left;
    letter-spacing: 2px;
    grid-row: 1 / 2;
    grid-column: 2/ 6;
  }
  /*Description*/
  :nth-child(4){
    margin:3%;
    justify-self: left;
    grid-row: 2 / 3;
    grid-column: 1 / 4;
  }
  /*Date*/
  :nth-child(3){
    font-size:1.1rem;
    justify-self: right;
    grid-row: 2 / 3;
    grid-column: 4 / 6;
  }
  /*Category*/
  :nth-child(5){
    grid-row: 3 / 4;
    grid-column: 1 / 2;
  }
  /*Priority*/
  :nth-child(6){
    grid-row: 3 / 4;
    grid-column: 2/ 3;
  }
  /*editButton*/
  :nth-child(7){
    font-size:1.4rem;
    justify-self: right;
    grid-row: 3 / 4;
    grid-column: 4 / 5;
  }
  /*trashButton*/
  :nth-child(8){
    font-size:1.4rem;
    grid-row: 3 / 4;
    grid-column: 5 / 6;
  }
}

.is-editing form{
    background-color: $dark; 
    border-radius:1rem;
    padding:3%;
    margin: 0 5% 2% 5%;
    display: grid;
    grid-auto-columns: 1fr 1fr 1fr 1fr;
    grid-auto-rows: auto 1fr;
 label {
    display: flex;
    flex-direction: column;
    margin:1rem auto .7rem auto;
    font-family: $fancyFont;
    text-shadow: 1px 1px $dark ;
    letter-spacing: 2px;
  }

  input {
    background: hsl(0, 0%, 97%);
    border: 1px solid hsl(0, 0%, 93%);
    font-size: 1.1rem;
    padding: 0.4em;
    border-radius: 0.25rem;
  }
  
  select {
    border: 1px solid hsl(0, 0%, 93%);
    font-size: 1.1rem;
    padding: 0.4em;
    border-radius: 0.25rem;
  }

  .buttonArea{
    text-align: center;
  }
  button{
    width:30%;
    padding:2%;
    margin:3%;
    background-color:$white;
    border: none;
    box-shadow: $dark 1px 1px 1px 1px;
    font-family:$fancyFont;
    font-size: 1rem;
    color:$dark;
    letter-spacing: 1px;    
    &:active{
      box-shadow: none;
      background-color:$pink;
    }
  } 


}


</style>
