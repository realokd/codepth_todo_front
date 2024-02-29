<script lang="ts">
  import LeftPanel from "../lib/leftPanel.svelte";
  import axios from "axios";
  import BaseUrl from "../utils/Baseurl";
  import Icon from "@iconify/svelte";

  let todoDescription = "";
  let editMode = false;
  let updateID = 0;

  let todos: {
    ID: number;
    Description: string;
    Completed: number;
  }[] = [];

  // const todo = { task: "", status: "INSERT", taskStatus: "PENDING" };

  const getTodos = async () => {
    try {
      const res = await axios.get(BaseUrl + "list");
      todos = res.data;
      console.log(res.data);
    } catch (e) {
      console.log(e);
    }
  };

  getTodos();

  const addTodo = async () => {
    if (todoDescription === "") return;
    try {
      const res = await axios.post(BaseUrl + "add", {
        Description: todoDescription,
        Status: "INSERT",
      });
      // todos = res.data;
      todoDescription = "";
      console.log(res.data);
      getTodos();
    } catch (e) {
      console.log(e);
    }
  };

  const deleteTodo = async (id: number) => {
    console.log("hello");
    try {
      const res = await axios.delete(BaseUrl + `delete/${id}`);
      getTodos();
      console.log(res.data);
    } catch (e) {
      console.log(e);
    }
    return;
  };
  const updateTodo = async () => {
    try {
      const res = await axios.post(BaseUrl + `update/${updateID}`, {
        Description: todoDescription,
      });
      editMode = false;
      todoDescription = "";
      getTodos();
      console.log(res.data);
    } catch (e) {
      console.log(e);
    }
  };

  const completeTodo = async () => {
    try {
      const res = await axios.post(BaseUrl + `update/${updateID}`, {
        Completed: 1,
      });
      getTodos();
      console.log(res.data);
    } catch (e) {
      console.log(e);
    }
  };
</script>

<div
  class=" grid grid-cols-5 grid-rows-1 w-full h-full bg-gradient-to-b from-blue-500 to-white"
>
  <!-- <div class=" h-full col-span-1"><LeftPanel /></div> -->
  <div
    class=" col-span-5 text-xl w-full flex flex-col items-center justify-start md:p-5 p-2 gap-y-8"
  >
    <div class="flex justify-center gap-x-1 w-full md:text-lg text-[1rem]">
      <input
        class=" md:w-4/6 w-5/6 rounded-xl py-1 bg-slate-200 px-4"
        type="text"
        placeholder="Type todo here..."
        bind:value={todoDescription}
      />

      {#if !editMode}
        <button on:click={addTodo} class=" bg-green-500 rounded-xl py-2 px-3">
          Add
        </button>
      {:else}
        <button
          on:click={updateTodo}
          class=" bg-green-500 rounded-xl py-2 px-3"
        >
          Edit
        </button>
      {/if}
    </div>
    <div class=" flex flex-col justify-start items-center w-full">
      {#each todos as { Description, ID, Completed }, index (ID)}
        <div
          class="border-t-2 border-solid border-zinc-500 md:w-4/6 flex justify-around items-center gap-x-5 w-full"
        >
          <span
            class={`mb-1 rounded-md p-1 h-4 w-4 mt-1 ${
              Completed === 1 ? " bg-green-600" : " bg-red-500"
            }`}
          ></span>
          <span
            class="description md:maa-w-[28rem] max-w-96 overflow-scroll whitespace-nowrap md:text-lg text-sm text-left flex-[4] no-scrollbar"
            >{Description}</span
          >
          <span
            class=" flex items-center justify-center md:gap-x-4 gap-x-1 relative right-1"
          >
            <button on:click={() => deleteTodo(ID)}>
              <Icon
                icon="material-symbols:delete-sweep-outline"
                class=" text-2xl cursor-pointer"
              />
            </button>
            <button
              on:click={() => {
                editMode = true;
                todoDescription = Description;
                updateID = ID;
              }}
            >
              <Icon
                icon="material-symbols:edit-outline-rounded"
                class=" text-2xl cursor-pointer hover:bg-slate-300 hover:scale-110 p-[0.1rem] transition-all rounded-full"
              />
            </button>
            {#if Completed === 0}
              <button
                on:click={() => {
                  editMode = false;
                  updateID = ID;
                  completeTodo();
                }}
              >
                <Icon
                  icon="material-symbols:check-small-rounded"
                  class=" text-2xl cursor-pointer hover:bg-slate-300 hover:scale-110 p-[0.1rem] transition-all rounded-full"
                />
              </button>
            {:else}
              <button class=" invisible">
                <Icon
                  icon="material-symbols:check-small-rounded"
                  class=" text-2xl cursor-pointer hover:bg-slate-300 hover:scale-110 p-[0.1rem] transition-all rounded-full"
                />
              </button>
            {/if}
          </span>
        </div>
      {/each}
    </div>
  </div>
</div>

<style>
  .description::-webkit-scrollbar {
    display: none;
  }

  .description {
    -ms-overflow-style: none;
    scrollbar-width: none; 
  }
</style>
