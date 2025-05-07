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

<h1 class="title">SimplyPut Example App</h1>
<p class="description">Ask a question and get an answer!</p>

{#if threadId}
	<p class="thread-id">Thread ID: {threadId}</p>
{/if}

{#if questions.length > 0}
	<section class="mb-4">
		{#each questions as q}
			<div class="question">
				<h2 class="text-red">{q.questionText}</h2>
				<Question questionId={q.questionId} />
			</div>
		{/each}
	</section>
{/if}

<section>
	<form class="form" on:submit={askQuestion}>
		<input class="input" type="text" bind:value={currentQuestion} placeholder="ask a question..." />
		<button class="button" type="submit">Submit</button>
	</form>
</section>
