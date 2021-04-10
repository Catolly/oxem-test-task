<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row justify="center">
          <v-col xl="6" class="pt-8">

          <template v-if="users.length === 0">
            <app-users-load 
              :URLs="datasetsURLs"
              :headers="datasetsHeaders"
              @load="setUsers"
            />
          </template>

          <template v-else>

            <v-expand-transition>
              <app-add-user-form 
                v-show="isShowAddUserForm"
                @submit="submitAddUserForm"
                @cancel="toggleAddUserForm"
              />
            </v-expand-transition>

            <!-- topbar -->
            <v-col class="px-0 d-flex align-center">
              <v-btn
                :disabled="isShowAddUserForm" 
                @click="toggleAddUserForm"
              >
                Добавить
              </v-btn>

              <span
                v-if="isShowSubmitLabel"
                class="ml-4 success--text"
              >
                Пользователь успешно добавлен!
              </span>

              <v-spacer />

              <v-col lg="6" class="pa-0">
                <app-filter-bar 
                  class="d-flex ml-auto"
                  v-model.trim="filterText"
                />
              </v-col>
            </v-col>

            <app-table
              :page="page"
              :dataPerPage="dataPerPage"
              :headers="tableHeaders" 
              :keys="tableKeys" 
              :items="filteredTableItems"
              @click="selectItem"
              @reset="resetPage"
            />

            <app-pagination
              :page="page"
              @change="changePage"
              :length="paginationLength"
              class="mt-8"
            />

            <app-user-card
              v-if="selectedItem"
              :firstName="selectedItem.firstName || ''"
              :lastName="selectedItem.lastName || ''"
              :description="selectedItem.description || ''"
              :address="selectedItem.address || {}"
              class="mt-8"
            />

            </template>

          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import AppTable from '@/components/AppTable'
import AppUserCard from '@/components/AppUserCard'
import AppPagination from '@/components/AppPagination'
import AppFilterBar from '@/components/AppFilterBar'
import AppAddUserForm from '@/components/AppAddUserForm'
import AppUsersLoad from '@/components/AppUsersLoad'

export default {
  name: 'App',

  components: {
    AppTable,
    AppUserCard,
    AppPagination,
    AppAddUserForm,
    AppFilterBar,
    AppUsersLoad,
  },

  data: () => ({
    selectedItem: null,
    
    dataPerPage: 50,
    page: 2,
    
    filterText: '',
    
    isShowAddUserForm: false,
    isShowSubmitLabel: false,
    
    datasetsURLs : [
      // Малый набор данных
      'http://www.filltext.com/?rows=32&id={number|1000}&firstName={firstName}&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}',

      // Большой набор данных
      'http://www.filltext.com/?rows=1000&id={number|1000}&firstName={firstName}&delay=3&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}',
    ],

    datasetsHeaders: [
      'Малый набор (32 пользователя)',
      'Большой набор (1000 пользователей)',
    ],

    users: [],

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

  computed: {
    paginationLength: function() {
      return Math.ceil(this.filteredTableItems.length / this.dataPerPage)
    },
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
      this.resetPage()
    },
  },

  methods: {
    selectItem(id) {
      this.selectedItem = this.users.find(user => (user.id === id))
    },

    resetPage() {
      this.page = 1
    },
    changePage(page) {
      this.page = page
    },

    submitAddUserForm(user) {
      this.addUser(user)
      this.toggleAddUserForm() 
      this.showSubmitLabel()
    },
    addUser(user) {
      this.users.push(user) 
    },
    toggleAddUserForm() {
      this.isShowAddUserForm = !this.isShowAddUserForm
    },
    showSubmitLabel() {
      this.isShowSubmitLabel = true
      setTimeout(() => {
        this.isShowSubmitLabel = false
      }, 5000)
    },

    setUsers(users) {
      this.users = users
    },
  },
}
</script>

<style>
li {
  list-style-type: none;
}
</style>