<script lang="ts">
  import TasksForm from "./lib/tasks-form.svelte";
  import TasksList from "./lib/tasks-list.svelte";
  import type { Task } from "./types";

  let tasks = $state<Task[]>([]);
  let totalDone = $derived(
    tasks.reduce(
      (acc, task) => acc + (task.done ? 1 : 0), 0
    )
  );

  function addTask(newTask: string) {
    tasks.push({
      id: crypto.randomUUID(),
      title: newTask,
      done: false,
    });
  }

  function toggleDone(task: Task) {
    task.done = !task.done;
  }
</script>

<main>
<TasksForm {addTask}/>
<p>{totalDone} / {tasks.length} Tasks completed</p>
<TasksList {tasks} {toggleDone}/>

</main>

<style>
  main {
    margin: 1rem auto;
    max-width: 800px;
  }
</style>
