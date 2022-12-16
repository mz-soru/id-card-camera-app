<script lang="ts">
	import TestComponent from "./TestComponent.svelte";
	let number = 0
	$: plusNumber = 'asd' + number
	const increase = ()=>{number+=1}
	const reset = ()=>{number=0}
	$: numbers = [number*2,number*4,number*8]
</script>

<main>
	<button on:click={increase}>button</button>
	<TestComponent number={number} reset={reset}/>
	<p>{number}</p>
	<p>{plusNumber}</p>
	{#each numbers as num}
		{#if num%3===0}
			<p>{num}</p>
		{/if}
	{/each}
</main>
