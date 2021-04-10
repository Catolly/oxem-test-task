<template>
	<div class="d-flex align-center">
		<v-text-field 
			label="Поиск"
			v-model="filterText"
			@change="$emit('input', filterText)"
		></v-text-field>
		<v-btn
			class="ml-8" 
			@click="$emit('input', filterText)"
		>
			Найти
		</v-btn>
	</div>
</template>

<script>
export default {
	name: 'AppSearchBar',

	data: () => ({
		filterText: '',
	}),
}
</script>