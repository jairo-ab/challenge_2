<template>
  <div class="title">
    <b-navbar toggleable="lg" type="dark" variant="info">
      <b-navbar-brand class="ml-2">
        <b>{{ appName }}</b>
      </b-navbar-brand>
    </b-navbar>
		<main class="content">
			<TitleDesc :title="'Como é o frete que você precisa?'" />
			<div class="container">
				<SubTitle :descricao="'Destino'" />
				<InputSelect :options="filterUniqueCity" />
			</div>
			<div class="container mt-4">
				<SubTitle :descricao="'Peso'" />
				<FormInput :placeholder="'Insira aqui o peso da carga em Kg'" type="number"/>
			</div>
			<div class="container d-flex justify-content-center">
				<b-button variant="success" size="lg" @click="calcular" >Analisar</b-button>
			</div>
			<div v-if="showFrete" class="container">
				<TitleDesc :title="'Melhores alternativas de frete que encontramos para você.'" />
				<CardInfo :descricao="freteRapido.descricao" :transport="freteRapido.transport" :icon="freteRapido.icon" :color="freteRapido.color" :alt="freteRapido.alt"/>
				<br>
				<CardInfo :descricao="freteBarato.descricao" :transport="freteBarato.transport" :icon="freteBarato.icon" :color="freteBarato.color" :alt="freteBarato.alt"/>
			</div>
		</main>
  </div>

</template>

<script>
import {
  BNavbar,
  BNavbarBrand,
} from 'bootstrap-vue'

import { 
	TitleDesc,
	InputSelect,
	SubTitle,
	FormInput,
	CardInfo
} from './bosons'

import { bus } from '../main.js'

export default {
  components: {
    BNavbar,
    BNavbarBrand,
		TitleDesc,
		InputSelect,
		SubTitle,
		FormInput,
		CardInfo
  },
  data() {
    const appName = ''

    return {
      appName,
			transposts: [],
			city: '',
			peso: '',
			freteRapido: {},
			freteBarato: {},
			showFrete: false
    }
  },
  created() {
    // Implemente aqui o GET dos dados da API REST
    // para que isso ocorra na inicialização da pagina
		this.appName = 'Melhor Frete'

		bus.$on('updateCity', response => {
			this.city = response
		})

		bus.$on('updatePeso', response => {
			this.peso = response
		})

		this.axios.get('/transport')
		.then(res => {
			this.transposts = res.data
		})
		.catch(err => {
			console.warn(err)
		})
  },
	computed: {
		// Irá remover as cidades com os nomes repetidos - reduzir
		filterUniqueCity(){
			// cria um array apernas com os nomes das cidades
			const city = this.transposts.map(value => value.city)
			
			// Remove os valores repetidos
			const noRepeated = city.reduce((array, value) => {
				return array.includes(value) ? array : [...array, value];
			}, []);

			// criando um objeto de retorno para alimentar nosso select-input
			const createObj = noRepeated.map((value, index) => { 
				return { index, value }
			})

			return createObj
		}
	},
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    methodFoo() {
      console.log(this.appName)
    },

		calcular(){
			// validações
			if(!this.city || !this.peso)
				return alert('Nem todos os campos forem preenchidos!')

			if(this.city.includes('Selecione'))
				return alert('Necessário informar a cidade de destino')

			// filtrando as cidades
			const cities = this.methodFilter(this.city)

			//filtrando os valores
			const values = this.methodCalc(cities, this.peso)

			// filtrando frete mais rápido
			const fastLeadTime = this.methodFastLeadTime(values)

			// filtrando frete mais barato
			const priceShippin = this.methodSearchByShippingPrice(values)

			// alimentando DOM
			this.freteRapido = {
				icon: 'clock.svg',
				descricao: 'Frete mais rápido',
				transport: `${fastLeadTime.name} - ${fastLeadTime.estimated_freight.toLocaleString('pt-br',{style: 'currency', currency: 'BRL'})} - ${fastLeadTime.lead_time}h`,
				color: 'color-primary',
				alt: 'Frete rápido'
			}

			this.freteBarato = {
				icon: 'money.svg',
				descricao: 'Frete mais barato',
				transport: `${priceShippin.name} - ${priceShippin.estimated_freight.toLocaleString('pt-br',{style: 'currency', currency: 'BRL'})} - ${priceShippin.lead_time}h`,
				color: 'color-secondary',
				alt: 'Frete barato'
			}
			
			this.showFrete = true
		},

		// metodo para buscar somente as cidades de acordo com a selecionada
		methodFilter(destiny){
			const transpostsList = this.transposts
			const filter = transpostsList.filter(value => {
				return value.city === destiny
			})

			return filter
		},

		// metodo para calcular os precos de acordo com o peso informado
		methodCalc(shipping, weight){
			const calculation = shipping.map(value => {
				const { id, name, city } = value

				if(weight <= 100){
					return {
						id,
						name,
						cost_transport_light: parseFloat(value.cost_transport_light.replace('R$', '')),
						cost_transport_heavy: parseFloat(value.cost_transport_heavy.replace('R$', '')),
						city,
						lead_time: parseInt(value.lead_time.replace('h', '')),
						estimated_freight: parseFloat(value.cost_transport_light.replace('R$', '')) * weight
					}
				}

				if(weight > 100){
					return {
						id,
						name,
						cost_transport_light: parseFloat(value.cost_transport_light.replace('R$', '')),
						cost_transport_heavy: parseFloat(value.cost_transport_heavy.replace('R$', '')),
						city,
						lead_time: parseInt(value.lead_time.replace('h', '')),
						estimated_freight: parseFloat(value.cost_transport_heavy.replace('R$', '')) * weight
					}
				}
			})

			return calculation
		},

		// metodo para verificar o frete mais rapido
		methodFastLeadTime(shipping){
			const order = shipping.sort((a, b)=> a.lead_time - b.lead_time)

			return order[0]
		},

		// metodo para buscar os fretes mais baratos
		methodSearchByShippingPrice(shipping){
			const order = shipping.sort((a, b)=> a.estimated_freight - b.estimated_freight)

			return order[0]
		}
  },
}
</script>

<style scoped>
.title .navbar {
  background-color: #00aca6 !important;
}

.title .navbar-brand {
  margin-left: 20px;
}

.content {
	width: 100%;
	display: grid;
	grid-template-columns: auto;
	grid-gap: 2rem;

	justify-content: center;
	align-items: center;
	margin: 1rem 0;
}

</style>
