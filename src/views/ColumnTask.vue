<template>
    <div
          class="task"
          draggable
          @dragstart="pickupTask($event,taskIndex,columnIndex)"
          @click="goToTask(task)"
          @dragover.prevent
          @dragenter.prevent
          @drop.stop="moveTaskOrColumn($event, column.tasks, columnIndex,  taskIndex)"
        >
          <span class="w-full flex-no-shrink font-bold">
            {{ task.name }}
          </span>
          <p
            v-if="task.description"
            class="w-full flex-no-shirnk text-sm"
          >
            {{ task.description }}
          </p>
    </div>
</template>

<script>
    export default {
        props: {
            column: {
                type: Object,
                required: true
            },
            columnIndex: {
                type: Number,
                required: true
            },
            taskIndex: {
                type: Number,
                require: true
            },
            task: {
                type: Object,
                require: true
            },
            board: {
                type: Object,
                require: true
            }
        },
        methods: {
            goToTask(task) {
                this.$router.push({name: 'task', params:{ id: task.id }})
            },
            pickupTask(e, taskIndex, fromColumnIndex){
                e.dataTransfer.effectAllowed = 'move'
                e.dataTransfer.dropEffect = 'move'

                e.dataTransfer.setData('from-task-index', taskIndex)
                e.dataTransfer.setData('from-column-index', fromColumnIndex)
                e.dataTransfer.setData('type', 'task')
            },
            moveTaskOrColumn(e, toTasks, toColumnIndex, toTaskIndex){
                const type = e.dataTransfer.getData('type')
                if (type === 'task') {
                    this.moveTasks(e, toTasks, toTaskIndex !== undefined ? toTaskIndex : toTasks.length)
                } else {
                    this.moveColumn(e, toColumnIndex)
                }
            },
            moveTasks(e, toTasks, toTaskIndex) {
                const fromColumnIndex = e.dataTransfer.getData('from-column-index')
                const fromTaskIndex = e.dataTransfer.getData('from-task-index')
                const fromTasks = this.board.columns[fromColumnIndex].tasks
                this.$store.commit('MOVE_TASK', {
                    fromTasks,
                    toTasks,
                    fromTaskIndex,
                    toTaskIndex
                })
            },
            moveColumn(e, toColumnIndex) {
                const fromColumnIndex = e.dataTransfer.getData('from-column-index')
                this.$store.commit('MOVE_COLUMN', {
                    fromColumnIndex,
                    toColumnIndex
                })
            }
        }
    }
</script>

<style scoped>
.task {
  @apply flex items-center flex-wrap shadow mb-2 py-2 px-2 rounded bg-white text-grey-darkest no-underline;
}
</style>