<script lang="ts">
  import TasksForm from "./lib/tasks-form.svelte";
  import TasksList from "./lib/tasks-list.svelte";
  import type { Filter, Task } from "./types";

  let currentFilter = $state<Filter>("all");
  let tasks = $state<Task[]>([]);
  let totalDone = $derived(
    tasks.reduce(
      (acc, task) => acc + (task.done ? 1 : 0), 0
    )
  );
  let filteredTasks = $derived.by(() => {
    switch (currentFilter) {
      case "all":
        return tasks;
      case "done":
        return tasks.filter(task => task.done);
      case "todo":
        return tasks.filter(task => !task.done);
    }
    return tasks;
  });

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

  function removeTask(id: string) {
    const index = tasks.findIndex(task => task.id === id);
    tasks.splice(index, 1);
  }
</script>

<main>
<TasksForm {addTask}/>
<p>
  {#if tasks.length === 0}
    All done!
  {:else}
    {totalDone} / {tasks.length} Tasks completed
  {/if}
</p>

{#if tasks.length}
  <div class="button-container">
    <button onclick={() => currentFilter = "all"} class:contrast={currentFilter === "all"} class="secondary">All</button>
    <button onclick={() => currentFilter = "todo"} class:contrast={currentFilter === "todo"} class="secondary">Todo</button>
    <button onclick={() => currentFilter = "done"} class:contrast={currentFilter === "done"} class="secondary">Done</button>
  </div>
{/if}
<TasksList tasks = {filteredTasks} {toggleDone} {removeTask}/>

</main>

<style>
  main {
    margin: 1rem auto;
    max-width: 800px;
  }

  .button-container {
    display: flex;
    justify-content: end;
    gap: 0.2rem;
    margin-bottom: 1rem;
  }
</style>
