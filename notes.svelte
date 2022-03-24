<script>
  /*Init JSDB */
  import jsdb from "@javascriptdb/jsdb-sdk";
  const {DatabaseArray} = jsdb('http://localhost:3001');

  /* Get notes from database */

  import {onMount} from "svelte";

  const notes = new DatabaseArray('notes');

  let activeNotes = [];

  async function getNotes() {
    activeNotes = await notes.filter(note => note.status === 'ACTIVE');
  }

  onMount(async () => {
    getNotes();
  })

  /* Add new notes */

  let content;

  async function saveNote() {
    await notes.push({
      content,
      date: new Date(),
      status: 'ACTIVE'
    });
    content = undefined;
    getNotes();
  }

  /* Archive notes */

  async function archiveNote(index, note) {
    activeNotes[index] = {...note, status: 'ARCHIVED'};
    activeNotes = activeNotes.filter(note => note.status === 'ACTIVE');
  }
</script>

<section class="p-8 bg-gray-100">
    <label htmlFor="note" class="block text-lg font-medium text-gray-700">New note</label>
    <div class="my-4">
        <textarea bind:value={content} rows="4" type="note" name="note" id="note"
                  class="shadow-md focus:ring-indigo-500 focus:border-indigo-500 w-full border-gray-100 border rounded-md"></textarea>
    </div>
    <button on:click={saveNote} type="button"
            class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
        Save
    </button>
</section>

<section class="p-8">
    {#each activeNotes as note, index (note._id)}
        <div class="shadow-sm border border-gray-200 rounded p-4 mb-4">
            <p class="text-sm text-gray-400 mt-2">
                {(new Date(note.date)).toLocaleString()}
            </p>
            <p>{note.content}</p>
            <p on:click={()=> archiveNote(index, note)} class="text-sm text-red-400 mt-2 cursor-pointer">Archive</p>
        </div>
    {/each}
</section>
