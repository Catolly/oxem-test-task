<template>
	<div>
		<app-table
      :page="currentPage"
      :dataPerPage="dataPerPage"
      :headers="headers" 
      :keys="keys" 
      :items="items"
      @click="emitItemId"
      @reset="resetPage"
    />

    <div class="mt-8 d-flex align-center">
      <app-pagination
        :page="currentPage"
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

	data: () => ({
		currentPage: 1,
		dataPerPage: 5,
	}),

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
          this.dataPerPage = this.maxDataPerPage

        else if (dataPerPage < 1)
          this.dataPerPage = 1  
        
        else this.dataPerPage = dataPerPage
      }
    },
    paginationLength: function() {
      return Math.ceil(this.items.length / this.dataPerPage)
    },
	},

	watch: {
		page() {
			this.currentPage = this.page
		},
	},

	methods: {
		resetPage() {
			this.currentPage = 1
		},
		changePage(page) {
			this.currentPage = page
		},
		emitItemId(id) {
			this.$emit('click', id)
		},
	},
}
</script>