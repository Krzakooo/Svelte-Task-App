<script lang="ts">
  import TasksForm from "./lib/tasks-form.svelte";
  import TasksList from "./lib/tasks-list.svelte";
  import type { Task } from "./types";

  let tasks = $state<Task[]>([]);

  function addTask(newTask: string) {
    tasks.push({
      id: crypto.randomUUID(),
      title: newTask,
      done: false,
    });
  }
</script>

<main>
<TasksForm {addTask}/>
<TasksList {tasks}/>

</main>

<style>
  main {
    margin: 1rem auto;
    max-width: 800px;
  }
</style>
