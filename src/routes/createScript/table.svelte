<script>
import {supabase} from "../../supabase"
import {onMount} from "svelte"
import { currPlay } from '../../stores';
import {goto} from "$app/navigation";
import { bind } from "svelte/internal";

let uuid = supabase.auth.user()?.id;
let searchText = "";
let possibilities = [];
let playQuant = 0;
let currentWord;

  function changeSearch (cell) {
    searchText = cell;
    console.log(searchText)
    console.log(tabledata)
  }
  async function textSearch () {
    const { data, error } = await supabase
  .from('playbook')
  .select('name')
  .ilike('name', "%" + searchText + "%")
   possibilities = data;
   playQuant = data?.length;
   console.log(possibilities)
}

function addRow() {
		tabledata = [...tabledata, [...newRow]]
		newRow = columns
	}
	function deleteRow(rowToBeDeleted) {
		tabledata = tabledata.filter(row => row != rowToBeDeleted)
	}
    
	let columns = ["Play Name", "Down and Distance", "Hash","Def Call"]
	let tabledata = [
    ["#######", "#######", "#######" , "####### "],
    ["#######", "#######", "#######" , "#######"],
    ["#######", "#######", "(#######", "#######"]
  ]
	let newRow = [...columns];

    let items = [];
	let filterText = '';
	
</script>
<div class="overflow-x-auto shadow-2xl rounded-b-2xl mx-6">

    <table class="table table-compact table-zebra w-full self-center text-center ">
        <thead>
            {#each columns as column}
                <th>{column}</th>
            {/each}
        </thead>
        <tbody>
        {#each tabledata as row}
            <tr>
                {#each row as cell}
                        <th contenteditable="true" bind:innerHTML={cell} class="text-l font-semibold" on:keypress={changeSearch(cell)} on:keypress={textSearch}>
                            {#if playQuant > 0}

                            {/if}
                    </th>
                {/each}
                <button on:click={() => deleteRow(row)}>X</button>
            </tr>
        {/each}
        <tr class = "opacity-80">
            {#each newRow as column}
                <td contenteditable="true" bind:innerHTML={column} />
            {/each}
            <button on:click={addRow}>add</button>
        </tr>
    </tbody>
    </table>
</div>