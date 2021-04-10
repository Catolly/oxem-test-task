<template>
	<div>
		<v-expand-transition>
			<app-add-user-form 
				v-show="isShowAddUserForm"
				@submit="submitAddUserForm"
				@cancel="toggleAddUserForm"
			/>
		</v-expand-transition>

		<app-topbar 
			v-model="filterText"
			:disabled="isShowAddUserForm"
			@click="toggleAddUserForm"
			@success="showSubmitLabel"
			@successTimeout="hideSubmitLabel"
			@successText="'Пользователь успешно добавлен!'"
		/>

		<app-pagination-table 
			:headers="tableHeaders" 
			:keys="tableKeys" 
			:items="filteredTableItems"
			:page="page"
      :dataPerPage="dataPerPage"
			:maxDataPerPage="50"
			@click="selectItem"
      @changePage="changePage"
      @changeDataPerPage="changeDataPerPage"
		/>

		<app-user-card
			v-if="selectedItem"
			:firstName="selectedItem.firstName || ''"
			:lastName="selectedItem.lastName || ''"
			:description="selectedItem.description || ''"
			:address="selectedItem.address || {}"
			class="mt-8"
		/>
	</div>
</template>

<script>
import AppAddUserForm from '@/components/AppAddUserForm'
import AppTopbar from '@/components/AppTopbar'
import AppPaginationTable from '@/components/AppPaginationTable'
import AppUserCard from '@/components/AppUserCard'

export default {
	name: 'AppMain',

	components: {
		AppAddUserForm,
		AppTopbar,
		AppPaginationTable,
		AppUserCard,
	},

	data: () => ({
		selectedItem: null,
    
    page: 1,
    dataPerPage: 10,
    
    filterText: '',
    
    isShowAddUserForm: false,
    isShowSubmitLabel: false,

    tableHeaders: [
      'Id',
      'First Name',
      'Last Name',
      'Email',
      'Phone',
    ],
    tableKeys: [
      'id',
      'firstName',
      'lastName',
      'email',
      'phone',
    ],
	}),

	props: {
		users: {
			type: Array,
			required: true,
		},
	},

	computed: {
		tableItems: function() {
      if (this.users.length === 0) return []

      return this.users.map(user => (
          this.tableKeys.reduce((acc, key) => (
            acc[key] = user[key], acc
          ), {})
      ))
    },
    filteredTableItems() {
      if (this.users.length === 0) return []
      if (!this.filterText) return this.tableItems

      return this.tableItems.filter(item =>
        Object.values(item).filter(
          value => value.toString()
                        .toLowerCase()
                        .includes(this.filterText)
        ).length != 0
      )
    },
	},

	watch: {
    filterText() {
      this.filterText = this.filterText.toLowerCase()
      this.page = 1
    },
  },

  methods: {
		selectItem(id) {
      this.selectedItem = this.users.find(user => (user.id === id))
    },
    changePage(page) {
      this.page = page
    },
    changeDataPerPage(dataPerPage) {
      this.dataPerPage = dataPerPage
    },

    submitAddUserForm(user) {
      this.$emit('addUser', user)
      this.toggleAddUserForm() 
      this.showSubmitLabel()
    },
    toggleAddUserForm() {
      this.isShowAddUserForm = !this.isShowAddUserForm
    },

    showSubmitLabel() {
      this.isShowSubmitLabel = true
    },
    hideSubmitLabel() {
      this.isShowSubmitLabel = false
    },
  }
}
</script>