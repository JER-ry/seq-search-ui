<script context="module">
  export const prerender = true
</script>

<script>
  import { goto } from "$app/navigation"
  import "../app.css"
</script>

<div class="flex flex-col px-12 lg:px-48 py-2 w-screen min-h-screen">
  <nav class="flex flex-row p-2 items-end space-x-6 border-b-2 text-gray-800">
    <button
      class="grow font-semibold text-xl text-left"
      on:click={() => {
        goto("/")
      }}
    >
      <h1>Ngram <span class="text-green-500">Lab</span></h1>
    </button>
  </nav>
  <div class="grow flex p-2">
    <slot />
  </div>
</div>
