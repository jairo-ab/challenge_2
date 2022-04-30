<template>
	<div class="sub-title">
		<p>{{ descricao }}</p>
	</div>
</template>

<script>
export default {
	props: {
		descricao: {
			type: String,
			required: true
		}
	}
}
</script>

<style scoped>
	.sub-title {
		position: relative;
		width: max-content;
	}

	p {
		font-size: 1.6rem;
	}
</style>>
