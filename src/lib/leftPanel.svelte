<script>
    let routes = [
      {
        name: "Pending Todos",
        route: "1",
      },
      {
        name: "Completed Todos",
        route: "2",
      },
    ];
  </script>
  
  <div class="h-full w-full flex flex-col gap-y-1 border-2 border-solid border-zinc-300 p-4">
      {#each routes as {name, route}, index (route)}
          <p class="w-full">{name}</p>
      {/each}
  </div>
  