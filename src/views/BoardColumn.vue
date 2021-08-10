<template>
    <div 
        class="column" 
        draggable
        @drop="moveTaskOrColumn($event, column.tasks,columnIndex)"
        @dragover.prevent
        @dragenter.prevent
        @dragstart.self="pickupColumn($event,columnIndex)"
    >
      <div class="flex flex-center mb-2 font-bold">
        {{ column.name }}
      </div>
      <div class="list-reset">
        <ColumnTask 
          v-for="(task,$taskIndex) in column.tasks"
          :key="$taskIndex"
          :task="task"
          :taskIndex="$taskIndex"
          :columnIndex="columnIndex"
          :column="column"
          :board="board"
        />
        <input 
          type="text"
          class="block p-2 w-full bg-transparent"
          placeholder="+ Enter new task"
          @keyup.enter="createTask($event,column.tasks)"
        >
      </div>
    </div>
</template>

<script>
import ColumnTask from './ColumnTask.vue';
import movingMixins from '@/mixins/movingMixin'
    export default {
        mixins: [movingMixins],
        components: {
            ColumnTask
        },
        methods: {
            createTask(e, tasks){
                this.$store.commit('CREATE_TASK', {
                    tasks,
                    name: e.target.value
                })
                e.target.value = ''
            },
            pickupColumn(e, fromColumnIndex){
                e.dataTransfer.effectAllowed = 'move'
                e.dataTransfer.dropEffect = 'move'

                e.dataTransfer.setData('from-column-index', fromColumnIndex)
                e.dataTransfer.setData('type', 'column')
            }
        }
    }
</script>

<style scoped>
.column {
  @apply bg-grey-light p-2 mr-4 text-left shadow rounded;
  min-width: 350px;
}
</style>