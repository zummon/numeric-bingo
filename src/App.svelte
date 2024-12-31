<script>
	import { onMount } from 'svelte'

	let colorPalette = [
		"bg-red-500",
		"bg-green-500",
		"bg-blue-500",
		"bg-pink-500",
		"bg-orange-500",
		"bg-emerald-500",
		"bg-indigo-500",
		"bg-rose-500"
	]
	let players = $state([
		{
			board: Array.from({ length: 5 }).map(() => Array.from({ length: 5 }))
		}
	])
	let panelRange = [
		Array.from({ length: 15 }, (_, i) => i + 1),
		Array.from({ length: 15 }, (_, i) => i + 16),
		Array.from({ length: 15 }, (_, i) => i + 31),
		Array.from({ length: 15 }, (_, i) => i + 46),
		Array.from({ length: 15 }, (_, i) => i + 61)
	]
	let host = $state([])
	let cycle = $state(0)

	function shuffle (array) {
		// gemini google - js sort random
		return array.sort(() => Math.random() - 0.5);
	}
	function restart () {
		players.forEach((player, i) => {
			player.board.forEach((boxes, index) => {
				players[i].board[index] = shuffle(panelRange[index]).slice(0,5);
			});
		});
		host = shuffle(Array.from({ length: 75 }, (_, i) => i + 1));
	}

	onMount(() => {
		const object = JSON.stringify(players[0]);
		Array.from({ length: 5 }).forEach(() => {
			players.push(JSON.parse(object));
		});
		restart();
	})

</script>
	
<div class="flex justify-center flex-wrap gap-4 p-4">
	<button class="text-2xl font-bold bg-white text-cyan-900 py-1 px-2 rounded-full" onclick={() => { 
		if (players.length > 1) { players.pop(); }; 
	}}>
		-
	</button>
	<button class="text-2xl font-bold bg-white text-cyan-900 py-1 px-2 rounded-full" onclick={() => {
			let object = JSON.stringify(players[0])
			object = JSON.parse(object)
			object.board.forEach((boxes,index) => {
				object.board[index] = shuffle(panelRange[index]).slice(0,5)
			})
			players.push(object)
		}}>
		+
	</button>
	<button class="text-lg font-semibold bg-white text-cyan-900 py-1 px-2 rounded-full" onclick={() => { cycle += 1; }}>
		Play
	</button>
	<button class="text-lg font-semibold bg-white text-cyan-900 py-1 px-2 rounded-full" onclick={() => { restart(); cycle = 0; }}>
		Restart
	</button>
</div>

<div class="text-center">
	<details>
		<summary class="text-lg font-semibold text-yellow-400 border-2 rounded-full w-fit mx-auto list-inside px-2">{host[cycle]}</summary>
		<div class="pt-2">
			<ol class="list-decimal list-inside columns-5">
				{#each host as number}
					<li class={number == host[cycle] ? 'font-semibold text-yellow-400' : ''}>{number}</li>
				{/each}
			</ol>
		</div>
	</details>
</div>

<div class="flex flex-wrap justify-center gap-4 p-4">
	{#each players as player, index}
		<table class="text-center">
			<thead>
				<tr class={colorPalette[index]}>
					<td class="font-semibold" colspan={player.board.length}>{'Player ' + (index + 1)}</td>
				</tr>
			</thead>
			<tbody class="bg-white text-black">
				{#each player.board as boxes}
					<tr class="">
						{#each boxes as box}
							<td class="px-2 py-1 border font-semibold {host.slice(0, cycle + 1).includes(box) ? 'bg-yellow-400' : ''}">
								{box}
							</td>
						{/each}
					</tr>
				{/each}
			</tbody>
		</table>
	{/each}
</div>
