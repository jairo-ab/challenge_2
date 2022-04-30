<template>
  <div>
		<b-form-select 
			class="form-select form-control-sm" 
			v-model="selected" 
			:options="options" 
			text-field="value" 
			value-field="value"
			:value="options.city"
			size="sm"
			>
		</b-form-select>
  </div>
</template>

<script>
import { bus } from '@/main.js'

export default {
	data(){
		return {
			selected: 'Selecione uma cidade'
		}
	},
	props: {
		options: {
			type: Array,
			default: () => []
		}
	},
	watch: {
		selected: function(val){
			bus.$emit('updateCity', val)
		}
	}
}
</script>

<style scoped>

.form-select {
	height: 60px;
	background: #00aca6;

	width: 100%;
	position: relative;
	color: #fff;
	font-size: 1.5em;
}

.form-select:hover {
	background: darken(#00aca6, 5%);
}

</style>