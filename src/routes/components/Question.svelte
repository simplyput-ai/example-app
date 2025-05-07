<script lang="ts">
	import { onMount } from 'svelte';

	interface props {
		questionId: string;
	}

	interface Question {
		answerText: string;
		generatedSql?: string;
		resultsUrl?: string;
		state: string;
	}

	let { questionId }: props = $props();

	let q: Question | undefined = $state();

	let poller: number;

	const fetchQuestion = async () => {
		const res = await fetch(`https://rest.simplyput.ai/app/v1/question/${questionId}`, {
			method: 'GET',
			headers: {
				'Content-Type': 'application/json',
				'x-app-key': 'pk-rJ87sFMNgsYwzTDjRDjEndJY8SXfp'
			}
		});

		const data = await res.json();
		console.log(data);

		if (data.state == 'complete') {
			clearInterval(poller);
		}

		q = data;
	};

	onMount(() => {
		fetchQuestion();

		poller = setInterval(() => {
			fetchQuestion();
		}, 1000);
	});
</script>

<section class="mb-4 border border-gray-300 p-4">
	<small class="text-xs font-light"
		>Question ID: {questionId}
		{#if q}State: {q.state}{/if}</small
	>

	{#if q}
		<pre class="whitespace-pre-line">
{q.answerText}
        </pre>

		{#if q.generatedSql}
			<p class="my-2 text-sm">Generated SQL:</p>
			<pre class="whitespace-pre-line">
{q.generatedSql}
            </pre>
		{/if}

		{#if q.resultsUrl}
			<p class="my-2 text-sm">Results link:</p>
			<a
				class="text-blue-500 hover:underline"
				href={q.resultsUrl}
				target="_blank"
				rel="noopener noreferrer">click here</a
			>
		{/if}
	{/if}
</section>
