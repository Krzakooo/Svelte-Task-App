<script lang="ts">
  import TasksForm from "./lib/tasks-form.svelte";
  import TasksList from "./lib/tasks-list.svelte";
  import {slide} from "svelte/transition";
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

{#snippet filterButton(filter :Filter)}
<button onclick={() => currentFilter = filter} class:contrast={currentFilter === filter} class="secondary filterButton">{filter}</button>
{/snippet}

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
  <div transition:slide class="button-container">
    {@render filterButton("all")}
    {@render filterButton("todo")}
    {@render filterButton("done")}
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

  .filterButton {
    text-transform: capitalize;
  }
</style>
