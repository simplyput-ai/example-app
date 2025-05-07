<script lang="ts">
	import Question from './components/Question.svelte';

	interface CreateQuestionResponse {
		questionId: string;
		threadId: string;
	}

	interface Question {
		questionId: string;
		questionText: string;
	}

	let currentQuestion = '';
	let questions: Question[] = [];
	let threadId: string | undefined = undefined;

	const askQuestion = async () => {
		const res = await fetch('https://rest.simplyput.ai/app/v1/question', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json',
				'x-app-key': 'pk-rJ87sFMNgsYwzTDjRDjEndJY8SXfp'
			},
			body: JSON.stringify({
				question: currentQuestion,
				threadId: threadId ? threadId : undefined,
				metadata: {
					appUserId: 'demo-user'
				}
			})
		});

		const data: CreateQuestionResponse = await res.json();

		if (!threadId) {
			threadId = data.threadId;
		}

		const newQuestion: Question = {
			questionId: data.questionId,
			questionText: currentQuestion
		};

		questions = [...questions, newQuestion];
	};
</script>

<svelte:head>
	<title>Example App</title>
	<meta name="description" content="SimplyPut example app" />
</svelte:head>

<div class="mx-auto p-2">
	<h1 class="title my-3 font-semibold">SimplyPut Example App</h1>

	{#if threadId}
		<p class="text-sm">Thread ID: {threadId}</p>
	{/if}

	{#if questions.length > 0}
		<section class="my-4">
			{#each questions as q}
				<div class="question">
					<h2 class="text-red">{q.questionText}</h2>
					<Question questionId={q.questionId} />
				</div>
			{/each}
		</section>
	{/if}

	<section>
		<form class="w-1/3" on:submit={askQuestion}>
			<input type="text" bind:value={currentQuestion} placeholder="ask a question..." />
			<button type="submit">Submit</button>
		</form>
	</section>
</div>
