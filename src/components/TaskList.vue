<template>
    <transition-group name="list" tag="section" class="task-list">
      <task-list-item
      v-for="task in sortedList"
      :key="task.id"
      :task="task"
      :priorities="priorities"
      :categories="categories"
      @updateTask="updateTask"
      @toggleDone="toggleDone"
      @deleteTask="deleteTask"/>
    </transition-group>
</template>

<script>
import TaskListItem from '@/components/TaskListItem'
export default {
  props: ['tasks','reversed','priorities','categories','filteredTasks','filterOn'], 
  components: { TaskListItem },
  methods: {
    toggleDone (task) {
      this.$emit('toggleDone', task)
    },
    deleteTask (task) {
      this.$emit('deleteTask', task)
    },
    updateTask (task) {
      this.$emit('updateTask', task)
    }
  },
  computed: {
    sortedList:function () {
       let sortedTasks = []
      if (this.filteredTasks.length > 0 || this.filterOn == true){
       sortedTasks = [...this.filteredTasks]
      }else{
       sortedTasks = [ ...this.tasks ]
      }
       //sortedTasks = [ ...this.tasks ]
        sortedTasks.sort((a, b) => new Date(a.dueAt) - new Date(b.dueAt))
        if (this.reversed == true){
          return sortedTasks.reverse()
        } else {
          return sortedTasks
        }
    }
  }
}
</script>

<style lang="scss">
/*Colors*/
$white: #fce6f1;
$pink: #f6afce;
$dark: #12000c;

.list-move {
  transition: all 400ms ease-in-out forwards 50ms;
}
.list-enter {
  opacity: 0;
  transform: translateY(-2em);
}
.list-leave-active {
  position: absolute;
}
.list-leave-to {
  opacity: 0;
  transform: translateY(2em);
}

.task-list-item{
  border: $pink solid 3px;
}

</style>
