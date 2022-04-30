<template>
	<div class="card" :class="colorBg">
		<img :src="iconPath" :lt="alt">
		<span>{{ descricao }}</span>
		<span>{{ transport }}</span>
	</div>
</template>

<script>
export default {
	props: {
		icon: {
			type: String
		},
		descricao: {
			type: String
		},
		transport: {
			type: String
		},
		color: {
			type: String
		},
		alt: {
			type: String
		}
	},
	computed: {
    iconPath() {
      return require(`@/assets/${this.icon}`)
    },
		colorBg() {
			return this.color
		}
  }
}
</script>

<style scoped>

	.card {
		padding: 10px;

		border-radius: 5rem;
		border-style: dotted;

		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: row;
	}
	img {
    width: 23px;
    height: 23px;
  }

	span {
		margin-left: 0.7em;
	}

	span:nth-child(3) {
		font-weight: bolder;
	}

	@media (max-width: 650px){
		
		span:nth-child(2){
			display: none;
		}
	}
</style>