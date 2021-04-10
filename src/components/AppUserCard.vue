<template>
  <v-card class="pa-4 py-6">

    <v-card-subtitle class="py-0">Выбран пользователь</v-card-subtitle>
    <v-card-title class="pt-2 text-h4">{{fullName}}</v-card-title>
    
    <!-- description -->
    <template v-if="!!description">
      <p class="mt-2 ml-4">
        <b>Описание:</b>
      </p>
      <v-col 
        xl="8" 
        lg="6" 
        md="8"
        class="py-0"
      >
        <v-textarea 
          filled
          readonly
          class="py-0"
          :value="description"
        >
        </v-textarea>
      </v-col>
    </template>

    <!-- address info -->
    <ul v-if="!!address" class="pl-4">
      <li v-if="!!address.streetAddres">
        Адрес проживания: <b>{{address.streetAddress}}</b>
      </li>
      <li v-if="!!address.city">
        Город: <b>{{address.city}}</b>
      </li>
      <li v-if="!!address.state">
        Провинция/штат: <b>{{address.state}}</b>
      </li>
      <li v-if="!!address.zip">
        Индекс: <b>{{address.zip}}</b>
      </li>
    </ul>
    
  </v-card>
</template>

<script>
export default {
  name: 'AppUserCard',

  props: {
    firstName: String,
    lastName: String,
    description: String,
    address: {
      type: Object,

      streetAddres: String,
      city: String,
      state: String,
      zip: String,
    }
  },

  computed: {
    fullName: function() {
      return this.firstName + ' ' + this.lastName
    },
  },
}
</script>