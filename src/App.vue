<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row justify="center">
          <v-col xl="6" class="pt-8">

            <app-users-load 
              v-if="users.length === 0"
              :URLs="datasetsURLs"
              :headers="datasetsHeaders"
              @load="setUsers"
            />

            <app-main
              v-else 
              :users="users"
              @addUser="addUser"
            />

          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import AppUsersLoad from '@/components/AppUsersLoad'
import AppMain from '@/components/AppMain'

export default {
  name: 'App',

  components: {
    AppUsersLoad,
    AppMain,
  },

  data: () => ({
    users: [],
    datasetsURLs : [
      // small dataset
      'http://www.filltext.com/?rows=32&id={number|1000}&firstName={firstName}&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}',

      // big dataset
      'http://www.filltext.com/?rows=1000&id={number|1000}&firstName={firstName}&delay=3&lastName={lastName}&email={email}&phone={phone|(xxx)xxx-xx-xx}&address={addressObject}&description={lorem|32}',
    ],
    datasetsHeaders: [
      'Малый набор (32 пользователя)',
      'Большой набор (1000 пользователей)',
    ],
  }),

  methods: {
    setUsers(users) {
      this.users = users
    },
    addUser(user) {
      this.users.push(user)
    },
  },
}
</script>

<style>
li {
  list-style-type: none;
}
</style>