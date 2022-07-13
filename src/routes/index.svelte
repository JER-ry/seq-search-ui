<script>
  import { browser } from "$app/env"
  import Papa from "papaparse"
  import { saveAs } from "file-saver"

  let url = `http://localhost:8000`

  let input = null
  let table1Ngram = null
  let table2Doc = null
  let table1 = null
  let table2 = null
  let table3 = null

  async function get(endpoint) {
    const response = await fetch(endpoint, {
      headers: {
        "Content-Type": "application/json"
      }
    })
    return await response.json()
  }

  async function getTable1(input) {
    table1Ngram = null
    table2Doc = null
    // get(url + `/table1/${input}`).then((res) => { table1 = })
    table1 = [
      [0, "medium", 0.1, 0.1],
      [1, "social media", 0.1, 0.1],
      [2, "medium platform", 0.1, 0.1]
    ]
    table2 = null
    table3 = null
  }

  async function getTable2(ngram) {
    table1Ngram = ngram
    table2Doc = null
    // get(url + `/table2/${ngram}/${input}`).then((res) => { table2 = })
    table2 = [
      [0, "dkey1", 0.1, ngram + " xxx, xxx, xxx"],
      [1, "dkey2", 0.1, ngram + " xxx, xxx, xxx"]
    ]
    table3 = null
  }

  async function getTable3(doc) {
    table2Doc = doc
    // get(url + `/table3/${doc}`).then((res) => { table3 = })
    table3 = [
      [0, doc + " ngram1", 0.1],
      [1, doc + " ngram2", 0.1]
    ]
  }

  function csv(data) {
    if (browser)
      return new Blob([Papa.unparse(data)], { type: "text/csv;charset=utf-8;" })
  }
</script>

<div class="grow flex flex-col mt-4 space-y-8 text-gray-500">
  <form class="space-x-2" on:submit|preventDefault={getTable1}>
    <input
      class="w-72 border-b-2 focus:border-gray-500 focus:outline-none"
      type="text"
      placeholder="Querying ngrams matching ..."
      bind:value={input}
    />
    <button
      class="mt-2 w-fit text-green-400 uppercase hover:underline"
      type="submit"
    >
      Search
    </button>
  </form>
  {#if table1}
    <div class="flex flex-col">
      <div class="flex flex-row font-bold uppercase text-base">
        <div class="w-1/2 pr-1">Matched ngrams</div>
        <div class="w-1/4 pr-1">Term freq</div>
        <div class="w-1/4 pr-1">Doc freq</div>
      </div>
      {#each table1 as ngram (ngram[0])}
        <div class="flex flex-row pt-2 pb-1 border-b-2 hover:bg-gray-50">
          <div class="flex flex-row w-1/2 pr-1">
            {#if table1Ngram === ngram[1]}
              <div class="text-green-400 font-bold mr-1">></div>
            {/if}
            <button
              class={table1Ngram === ngram[1]
                ? "inline-flex content-start font-bold hover:underline"
                : "inline-flex content-start hover:underline"}
              on:click={() => getTable2(ngram[1])}
            >
              {ngram[1]}
            </button>
          </div>
          <div class="w-1/4 pr-1">{ngram[2]}</div>
          <div class="w-1/4 pr-1">{ngram[3]}</div>
        </div>
      {/each}
      <button
        class="mt-2 w-fit text-green-400 uppercase hover:underline"
        on:click={() => {
          saveAs(
            csv([["Matched ngrams", "Term freq", "Doc freq"]].concat(table1)),
            input + "_table1.csv"
          )
        }}
      >
        Download as CSV
      </button>
    </div>
  {/if}
  {#if table2}
    <div class="flex flex-col">
      <div class="flex flex-row font-bold uppercase text-base">
        <div class="w-1/2 pr-1">Matched doc</div>
        <div class="w-1/4 pr-1">Term freq</div>
        <div class="w-1/4 pr-1">Related ngrams</div>
      </div>
      {#each table2 as doc (doc[0])}
        <div
          class="flex flex-row pt-2 pb-1 border-b-2 hover:bg-gray-50 text-base"
        >
          <div class="flex flex-row w-1/2 pr-1">
            {#if table2Doc === doc[1]}
              <div class="text-green-400 font-bold mr-1">></div>
            {/if}
            <button
              class={table2Doc === doc[1]
                ? "inline-flex content-start font-bold hover:underline"
                : "inline-flex content-start hover:underline"}
              on:click={() => getTable3(doc[1])}
            >
              {doc[1]}
            </button>
          </div>
          <div class="w-1/4 pr-1">{doc[2]}</div>
          <div class="w-1/4 pr-1">{doc[3]}</div>
        </div>
      {/each}
      <button
        class="mt-2 w-fit text-green-400 uppercase hover:underline"
        on:click={() => {
          saveAs(
            csv(
              [["Matched doc", "Term freq", "Related ngrams"]].concat(table2)
            ),
            table1Ngram + "_table2.csv"
          )
        }}
      >
        Download as CSV
      </button>
    </div>
  {/if}
  {#if table3}
    <div class="flex flex-col">
      <div class="flex flex-row font-bold uppercase text-base">
        <div class="w-1/2 pr-1">Popular ngrams</div>
        <div class="w-1/2 pr-1">Term freq</div>
      </div>
      {#each table3 as ngram (ngram[0])}
        <div
          class="flex flex-row pt-2 pb-1 border-b-2 hover:bg-gray-50 text-base"
        >
          <div class="flex flex-row w-1/2 pr-1">{ngram[1]}</div>
          <div class="w-1/2 pr-1">{ngram[2]}</div>
        </div>
      {/each}
      <button
        class="mt-2 w-fit text-green-400 uppercase hover:underline"
        on:click={() => {
          saveAs(
            csv([["Popular ngrams", "Term freq"]].concat(table3)),
            table2Doc + "_table3.csv"
          )
        }}
      >
        Download as CSV
      </button>
    </div>
  {/if}
</div>
