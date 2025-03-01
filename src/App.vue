<script>
import { computed, reactive, toRefs } from "vue";
import HelloWorld from "./components/HelloWorld.vue";
import IconCheckCircle from "./components/IconCheckCircle.vue";
import IconCircle from "./components/IconCircle.vue";
import IconDelete from "./components/IconDelete.vue";
import IconEdit from "./components/IconEdit.vue";
export default {
  name: "App",
  components: {
    IconCheckCircle,
    IconCircle,
    IconDelete,
    IconEdit,
  },

  setup() {
    const state = reactive({
      currentView: "All",
      newTaskInput: "",
      taskList: [],
    });

    const taskViews = reactive({
      allTaskLength: computed(() => {
        return state.taskList.length;
      }),
      currentTasksLength: computed(() => {
        return state.taskList.filter((item) => item.complete === false).length;
      }),
      completedTasksLength: computed(() => {
        return state.taskList.filter((item) => item.complete === true).length;
      }),
    });

    const tasksInView = computed(() => {
      if (state.currentView == "Current") {
        return state.taskList.filter((item) => item.complete === false);
      } else if (state.currentView == "Completed") {
        return state.taskList.filter((item) => item.complete === true);
      } else {
        return state.taskList;
      }
    });

    const setView = (viewLabel) => {
      state.currentView = viewLabel;
    };

    const addTask = () => {
      state.taskList.push({
        complete: false,
        label: state.newTaskInput,
        id: Math.floor(Math.random() * 100 + 1),
      });
      state.newTaskInput = "";
    };
    const deleteTask = (taskId) => {
      const index = state.taskList.findIndex((task) => task.id == taskId);
      state.taskList.splice(index, 1);
    };
    const editTask = (taskId) => {
      const index = state.taskList.findIndex((task) => task.id == taskId);
      state.taskList[index].edit = !state.taskList[index].edit;
    };

    return {
      ...toRefs(state),
      ...toRefs(taskViews),
      tasksInView,
      addTask,
      deleteTask,
      editTask,
      setView,
    };
  },
};
</script>

<template>
  <main class="main-wrapper">
    <h1 class="page-title">Vue Your Todo</h1>
    <div class="new-task-wrapper">
      <input
        type="text"
        placeholder="Type a new todo item"
        class="new-task-input"
        v-model="newTaskInput"
        @keyup.enter="addTask"
      />
      <button class="new-task-button" @click="addTask">+ Add</button>
    </div>
    <nav>
      <ul class="tab-wrapper">
        <li class="tab-item is-active">
          <button class="tab-button" @click="setView('All')">
            All ({{ allTaskLength }})
          </button>
        </li>
        <li class="tab-item">
          <button class="tab-button" @click="setView('Current')">
            Current ({{ currentTasksLength }})
          </button>
        </li>
        <li class="tab-item">
          <button class="tab-button" @click="setView('Completed')">
            Completed ({{ completedTasksLength }})
          </button>
        </li>
      </ul>
    </nav>
    <ul class="task-list">
      <li
        v-for="taskItem in tasksInView"
        :key="taskItem.label"
        class="task-list-item"
      >
        <div class="task-list-checkbox-wrapper">
          <IconCheckCircle v-show="taskItem.complete" />
          <IconCircle v-show="!taskItem.complete" />
          <input
            type="checkbox"
            v-model="taskItem.complete"
            :checked="taskItem.complete"
            class="task-list-checkbox"
          />
        </div>
        <input
          v-if="taskItem.edit"
          class="task-list-edit-input"
          type="text"
          v-model="taskItem.label"
        />
        <p
          v-else
          class="task-list-text"
          :class="taskItem.complete ? 'is-complete' : ''"
        >
          {{ taskItem.label }}
        </p>
        <div class="task-list-cta">
          <p>
            <IconEdit
              class="task-list-cta-icon"
              @click="editTask(taskItem.id)"
            /><span class="sr-only">Edit</span>
          </p>
          <p>
            <IconDelete
              class="task-list-cta-icon"
              @click="deleteTask(taskItem.id)"
            /><span class="sr-only">Delete</span>
          </p>
        </div>
      </li>
    </ul>
  </main>
</template>


<style>
html {
  background-color: #fbfbfb;
}
.task-list {
  padding: 0;
}
.task-list-checkbox-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}
.task-list-checkbox {
  position: absolute;
  left: -3px;
  bottom: 2px;
  opacity: 0;
}
.is-complete {
  color: #6b6b6b;
  font-style: italic;
}
.task-list-edit-input {
  border: none;
  font-size: 16px;
}
.task-list-item {
  border: 1px solid #f6f6f6;
  box-shadow: 2px 2px 8px 0 #cfcfcf;
  border-radius: 8px;
  display: flex;
  align-items: center;
  padding: 0 16px;
  margin-bottom: 16px;
}
.task-list-item:hover {
  border: 1px solid #0631f8;
}
.task-list-cta {
  display: flex;
  column-gap: 16px;
}
.task-list-text {
  margin-left: 12px;
  font-weight: bold;
  flex: 1;
}
.task-list-cta-icon .icon-object {
  fill: #2d2d2d;
}
.task-list-cta-icon:hover .icon-object {
  fill: #0728bf;
}
.tab-item:hover .tab-button {
  color: #0728bf;
}

.task-list-checkbox-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}
.task-list-checkbox {
  position: absolute;
  left: -3px;
  bottom: 2px;
  opacity: 0;
}
.task-list {
  padding: 0;
}
.task-list-cta-icon {
  opacity: 0;
  transition: 0.2s opacity ease-in;
}
.task-list-cta-icon .icon-object {
  fill: #2d2d2d;
}
.task-list-cta-icon:hover .icon-object {
  fill: #0728bf;
}
.task-list-item {
  border: 1px solid #f6f6f6;
  box-shadow: 2px 2px 8px 0 #cfcfcf;
  border-radius: 8px;
  display: flex;
  align-items: center;
  padding: 0 16px;
  margin-bottom: 16px;
}
.task-list-item:hover {
  border: 1px solid #0631f8;
}
.task-list-item:hover .task-list-cta-icon,
.task-list-item:focus .task-list-cta-icon {
  opacity: 1;
}
.task-list-cta {
  display: flex;
  column-gap: 16px;
}
.task-list-edit-input,
.task-list-text {
  margin-left: 12px;
  font-weight: bold;
  flex: 1;
  border: 0;
  font-size: 16px;
}
.task-list-text.is-complete {
  color: #6b6b6b;
  text-decoration: line-through;
}
.new-task-wrapper {
  display: flex;
}
.new-task-input {
  padding: 16px;
  font-weight: 600;
  color: #2d2d2d;
  flex: 1;
  border-top-left-radius: 8px;
  border-bottom-left-radius: 8px;
  border: 1px solid #f6f6f6;
  box-shadow: 2px 2px 8px 0 #c0c0c0;
  letter-spacing: 1px;
  font-size: 1rem;
}
.new-task-input:hover {
  border: 1px solid #0631f8;
}
.new-task-input::placeholder {
  color: #959595;
}
.new-task-button {
  background-color: #0631f8;
  color: #fff;
  padding: 18px 24px;
  font-weight: 900;
  border: 0;
  border-top-right-radius: 8px;
  border-bottom-right-radius: 8px;
  transition: 0.2s background ease-in;
  font-size: 1rem;
}
.new-task-button:hover {
  background-color: #082ac9;
}
.main-wrapper {
  max-width: 600px;
  margin: 0 auto;
}

.tab-wrapper {
  display: flex;
  column-gap: 30px;
  list-style: none;
  margin: 45px 0;
  padding: 0;
}
.tab-item {
  padding-bottom: 6px;
}
.tab-item.is-active {
  border-bottom: 3px solid #0631f8;
}
.tab-button {
  border: 0;
  background-color: transparent;
  color: #6b6b6b;
  letter-spacing: 1px;
  font-weight: bold;
  font-family: "Source Sans Pro";
  padding: 0;
}
.tab-button:hover {
  cursor: pointer;
}
.task-list {
  padding: 0;
}
.task-list-item {
  border: 1px solid #f6f6f6;
  box-shadow: 2px 2px 8px 0 #cfcfcf;
  border-radius: 8px;
  display: flex;
  align-items: center;
  padding: 0 16px;
  margin-bottom: 16px;
}
.task-list-item:hover {
  border: 1px solid #0631f8;
}
.task-list-cta {
  display: flex;
  column-gap: 16px;
}
.task-list-text {
  margin-left: 12px;
  font-weight: bold;
  flex: 1;
}
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}
</style>