<template>
	<div>
		<b-form-input v-model="peso" class="form-input" :type="type" :placeholder="placeholder"></b-form-input>
	</div>
</template>

<script>
import { bus } from '@/main.js'

export default {
	data(){
		return {
			peso: ''
		}
	},
	props: {
		placeholder: {
			type: String
		},
		type: {
			type: String
		}
	},
	watch: {
		peso: function(val){
			bus.$emit('updatePeso', val)
		}
	}
}
</script>

<style scoped>
	.form-input {
		height: 60px;
		background: #00aca6;

		width: 500px;
		position: relative;
		color: #fff;
		font-size: 1.5em;
	}

	.form-input::placeholder {
		color: #fff;
	}

	.form-input:hover {
		background: darken(#00aca6, 5%);
		color: #fff;
	}

	.form-input:focus {
		background: #00aca6;
		color: #fff;
	}

	@media (max-width: 650px){
		.form-input {
			width: 100%;
		}
	}
</style>