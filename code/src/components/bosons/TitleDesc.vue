<template>
	<div class="title-desc">
		<h3>{{ title }}</h3>
	</div>
</template>

<script>
	export default {
		props: {
			title: {
				type: String,
				required: true
			}
		}
	}
</script>

<style scoped>
	.title-desc {
		position: relative;
		width: 420px;

		margin: 2em;
		border-bottom: 2px solid #333;
		word-break: break-word;
	}

	.title-desc > h3 {
		font-weight: bold;
	}

	@media (max-width: 650px){
		.title-desc  {
			width: 300px;
		}
	}
</style>