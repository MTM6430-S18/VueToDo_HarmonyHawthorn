<template>
  <header class="the-header">
    <h1 class="app-title">
      Vue ToDos
    </h1>
    <h3>Today is</h3>
    <h2 class="current-date">{{ (new Date()).toDateString() }}</h2>
    <div class="btnArea">
      <button @click="filterActive()">
        <span v-if="activeList">Incomplete</span>
        <span v-else>Complete</span>
      </button>
      <button @click="openNewTaskForm()">
        <span>New Task</span>   
      </button>
      <button  class="ascBtn" @click="toggleOrder()">
        <span v-if="desc">Asc</span>
        <span v-else>Desc</span>
      </button>
    </div>

    <div class="categoryArea">
      <label>
      Category
      </label>
      <select v-model="selectedCat" @change=filter()>
        <option value=""></option>
        <option v-for="category in categories" :key="category.name" >
            {{category.name}}
        </option>
      </select> 
    </div>    
    <div class="priorityArea">
      <label>
      Priority
      </label>
      <select v-model="selectedPri" @change=filter()>
        <option value=""></option>
        <option v-for="priority in priorities" :key="priority.name">
            {{priority.name}}
        </option>
      </select>
    </div>

  </header>

</template>

<script>
export default {
  name: 'the-header',
  props: ['priorities','categories'], 
  data: () => ({
    selectedCat: '',
    selectedPri: '',
    activeList: true,
    desc: true,
  }),
  computed: {

  },
  methods: {
    openNewTaskForm() {
      this.$parent.openNewTaskForm()
    },
    filterActive() {
      this.activeList = !this.activeList
      this.$parent.toggleActive()
    },
    toggleOrder() {
      this.$parent.reverseTasks()
      this.desc = !this.desc
    },
    filter() {
      this.$emit('filter',this.selectedPri,this.selectedCat)
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

.the-header {
  text-align: center;
  font-family: $fancyFont;
  letter-spacing: 3px;
  margin-top:4%;

  .app-title {
    align-items: center;
    display: flex;
    justify-content: center;
  }



//Today is...
  h3{
    font-size:1.1rem;
  } 

  button{
    width:30%;
    padding:2%;
    margin:3%;
    background-color:$white;
    border: none;
    box-shadow: $dark 1px 1px 1px 1px;
    color:$dark;
    letter-spacing: 1px;   
    span{
      font-size:1rem;
      font-family: $fancyFont;
    } 
    &:active{
      box-shadow: none;
      background-color:$pink;
    }
  }
  
  label{
    margin-bottom:3%;
    font-size:1.1rem;
    letter-spacing: 2px;
  } 
  select{
    background: hsl(0, 0%, 97%);
    border: 1px solid hsl(0, 0%, 93%);
    width:25%;
    padding: 0.4em;
    margin-bottom:3%;
    border-radius: 0.25rem;
  }


}//end header
</style>
