<template>
	<div>
		<app-table
      :page="page"
      :dataPerPage="dataPerPage"
      :headers="headers" 
      :keys="keys" 
      :items="items"
      @click="emitItemId"
      @reset="resetPage"
    />

    <div class="mt-8 d-flex align-center">
      <app-pagination
        :page="page"
        @change="changePage"
        :length="paginationLength"
      />

      <v-spacer />

      <v-col lg="3">
        <v-text-field
          label="Элементов на странице"
          type="number"
          v-model.trim.number="dataPerPageField"
          class="ml-8"
        />
      </v-col>
    </div>
	</div>
</template>

<script>
import AppTable from '@/components/AppTable'
import AppPagination from '@/components/AppPagination'

export default {
	name: 'AppPaginationTable',

	components: {
    AppTable,
    AppPagination,
	},

	props: {
		items: {
			type: Array,
		},
		keys: {
			type: Array,
		},
		headers: {
			type: Array,
		},
		page: {
			type: Number,
			default: 1,
		},
		dataPerPage: {
			type: Number,
			default: 5,
		},
		maxDataPerPage: {
			type: Number,
			default: 50,
		},
	},

	computed: {
		dataPerPageField: {
      get: function() {
        return this.dataPerPage
      },
      set: function(dataPerPage) {
        if (dataPerPage > this.maxDataPerPage)
          this.$emit('changeDataPerPage', this.maxDataPerPage)

        else if (dataPerPage < 1)
          this.$emit('changeDataPerPage', 1)
        
        else this.$emit('changeDataPerPage', dataPerPage)
      }
    },
    paginationLength: function() {
      return Math.ceil(this.items.length / this.dataPerPage)
    },
	},

	methods: {
		resetPage() {
			this.page = 1
		},
		changePage(page) {
			this.$emit('changePage', page)
		},
		emitItemId(id) {
			this.$emit('click', id)
		},
	},
}
</script>