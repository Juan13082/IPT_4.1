<script setup lang="ts">
import { computed, type Ref, ref } from "vue";

interface Task {
  text: string;
  done: boolean;
  high_priority: boolean;
}

const tasks: Ref<Task[]> = ref([
  { text: "Rasen mähen", done: false, high_priority: false },
  { text: "Französisch lernen", done: false, high_priority: true },
  { text: "Einkaufen", done: false, high_priority: true },
  { text: "Abfall rausbringen", done: true, high_priority: true },
]);
// hier wird die List mir den Tasks geholt
const tasksLength = computed(getListLength);

// hier wird die länge der gemachten Tasks geholt
const lengthOfDoneTasks = computed(getLengthOfDoneTasks);

//hier werden die Tasks sortiert mit sort
const sortedTasks = computed(
    () =>
        tasks.value.sort((task1, task2) =>
            task1.high_priority === task2.high_priority
                ? 0
                : task1.high_priority
                    ? -1
                    : 1,
        )
);
// diese funktion gibt mir die Länge der liste und gibt sie ins html weiter
function getListLength() {
  return tasks.value.length;
}
// wenn die checkbox aktiviert ist bei der Task wird wird die Zahl um 1 erhöht
function getLengthOfDoneTasks() {
  let length = 0;
  tasks.value.forEach((task) => {
    if (task.done) {
      length++;
    }
  });
  return length;
}
// hier wird die Taskeingabe leiste zurücksgesetzt
function getNewTask(): Task {
  return {
    text: "",
    done: false,
    high_priority: false,
  };
}
// das ist die Funktion für den Button der die Task löscht
function deleteTask(taskToDelete: Task) {
  tasks.value = tasks.value.filter((task) => task !== taskToDelete);
}

const newTask = ref(getNewTask());
// die Task wird mit dieser Funktion erstellt
function addTask() {
  console.log(newTask.value);
  tasks.value.push(newTask.value);
  newTask.value = getNewTask();
}
</script>

<template>
  <div class="container">
    <h2>Aufgabenliste</h2>

    <p>{{ lengthOfDoneTasks }} von {{ tasksLength }} Tasks sind erledigt</p>

    <div class="form-group">
      <label for="task">Neuer Task</label>
      <input
          class="form-control"
          id="task"
          type="text"
          v-model="newTask.text"
      />
    </div>

    <label for="prio">
      <input id="prio" type="checkbox" v-model="newTask.high_priority" />
      Hohe Priorität
    </label>

    <div class="form-actions">
      <button @click="addTask">Task hinzufügen</button>
    </div>

    <ul>
      <li
          v-for="task in sortedTasks"
          :class="{
          'is-done': task.done,
          'high-priority': task.high_priority,
        }"
      >
        <input type="checkbox" v-model="task.done" />
        {{ task.text }}
        <button @click="deleteTask(task)">🗑️</button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.is-done {
  text-decoration: line-through;
}

.high-priority {
  font-weight: bold;
  color: red;
}

.form-group {
  display: block;
}

.form-group label {
  display: block;
  margin-bottom: 2px;
}

.form-control {
  width: 100%;
  padding: 2px 5px;
  height: 32px;
  margin-bottom: 5px;
}

.form-actions {
  display: block;
  margin-top: 1rem;
  margin-bottom: 2rem;
}

.container {
  margin: 20px auto;
  max-width: 400px;
  width: 100%;
}
</style>
