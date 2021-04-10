<template>
  <v-col class="px-0 d-flex align-center">
    <v-btn
      :disabled="disabled" 
      @click="$emit('click')"
    >
      Добавить
    </v-btn>

    <span
      v-if="success"
      class="ml-4 success--text"
    >
      {{successText}}
    </span>

    <v-spacer />

    <v-col lg="6" class="pa-0">
        <!-- v-model.trim="filterText" -->
      <app-filter-bar 
        class="d-flex ml-auto"
        v-model.trim="filterText"
        @input="$emit('input', filterText)"
      />
    </v-col>
  </v-col>
</template>

<script>
import AppFilterBar from '@/components/AppFilterBar'

export default {
  name: 'AppTopbar',

  components: {
    AppFilterBar,
  },

  data: () => ({
    filterText: '',
  }),

  props: {
    success: {
      type: Boolean,
      default: false,
    },
    successTimeout: {
      type: Number,
      default: 3000,
    },
    successText: String,
    disabled: {
      type: Boolean,
      default: false,
    },
  },

  watch: {
    success() {
      setTimeout(this.$emit('successTimeout'), this.successTimeout)
    },
  },
}
</script>