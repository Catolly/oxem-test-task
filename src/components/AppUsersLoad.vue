<template>
	<v-card class="pa-12">
		<div class="d-flex justify-center">
			<v-card-title class="pt-0 text-h4">
				{{loading ? 'Загрузка...' : 'Загрузить набор данных'}}
			</v-card-title>
		</div>

		<div 
			v-if="!loading"
			class="my-6 d-flex justify-center"
		>
			<v-btn
				v-for="(header, index) in headers"
				:key="index"
				:class="{'ml-8': index != 0}"
				@click="loadDataset(URLs[index])"
			>
				{{header}}
			</v-btn>
		</div>

		<v-expand-transition>
			<v-col lg="6" class="pa-0 offset-3">
				<v-progress-linear 
					v-if="loading"
					indeterminate
					class="mt-8" 
				/>
			</v-col>
		</v-expand-transition>
	</v-card>
</template>

<script>
export default {
	name: 'AppUsersLoad',

	data: () => ({
		loading: false,
	}),

	props: {
		URLs: {
			type: Array,
			required: true,
		},
		headers: {
			type: Array,
			required: true,
		},
	},

	methods: {
		async loadDataset(URL) {
			this.loading = true
			
			await fetch(URL)
			.then(res => res.json())
			.then(dataset => this.$emit('load', dataset))

			this.loading = false
		},
	}
}
</script>