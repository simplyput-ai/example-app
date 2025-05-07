<script lang="ts">
	interface props {
		questionId: string;
	}

	let { questionId }: props = $props();
</script>

<section class="mb-4 border border-gray-300 p-4">
	<small class="text-xs font-light">Question ID: {questionId}</small>
</section>
