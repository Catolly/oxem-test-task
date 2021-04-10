<template>
	<form @submit.prevent="submit">
		<v-card
			class="pa-4 py-2"
			transition="dialog-top-transition"
		>
			<v-col>
				<v-card-title class="pa-0">Новый пользователь</v-card-title>

				<v-text-field
					label="id"
					v-model.number="form.id.value"
					:error-messages="idErrors"
					@change="touch(form.id)"
					required
				></v-text-field>
				
				<v-text-field
					label="firstName"
					v-model.trim="form.firstName.value"
					:error-messages="firstNameErrors"
					@change="touch(form.firstName)"
					required
				></v-text-field>
				
				<v-text-field
					label="lastName"
					v-model.trim="form.lastName.value"
					:error-messages="lastNameErrors"
					@change="touch(form.lastName)"
					required
				></v-text-field>
				
				<v-text-field
					label="email"
					v-model.trim="form.email.value"
					:error-messages="emailErrors"
					@change="touch(form.email)"
					required
				></v-text-field>
				
				<v-text-field
					label="phone"
					v-model.trim="form.phone.value"
					:error-messages="phoneErrors"
					hint="В формате (xxx)xxx-xxxx"
					@change="touch(form.phone)"
					required
				></v-text-field>
				
				<div class="d-flex mt-4">
					<v-btn 
						color="primary"
						:disabled="!formIsFilled"
						@click="submit"
					>
						Добавить
					</v-btn>
					<v-btn
						color="error"
						plain
						class="ml-auto"
						@click="cancel"
					>
						Отмена
					</v-btn>
				</div>

			</v-col>
		</v-card>
	</form>
</template>

<script>
export default {
	name: 'AppAddUserForm',

	data: () => ({
		form: {
			id: {
				value: '',
				isDirty: false,
			},
			firstName: {
				value: '',
				isDirty: false,
			},
			lastName: {
				value: '',
				isDirty: false,
			},
			email: {
				value: '',
				isDirty: false,
			},
			phone: {
				value: '',
				isDirty: false,
			},
		}
	}),

	computed: {
		formIsFilled: function() {
			return Object.values(this.form).every(field => !!field.value)
		},		
		formIsDirtyAll: function() {
			return Object.values(this.form).every(field => field.isDirty)
		},
		formHasErrors: function() {
			return this.formErrors.some(fieldErrors => fieldErrors.length != 0)
		},
		formErrors: function() {
			return [
				this.idErrors,
				this.firstNameErrors,
				this.lastNameErrors,
				this.emailErrors,
				this.phoneErrors,
			]
		},
		idErrors: function() {
			const errors = []	
			if (!this.form.id.isDirty) return errors
			if (!this.form.id.value) errors.push('Небходимо ввести id')
			if (Number.isNaN(Number(this.form.id.value))) errors.push('Id должен быть числом')
			return errors
		},
		firstNameErrors: function() {
			const errors = []	
			if (!this.form.firstName.isDirty) return errors
			if (!this.form.firstName.value) errors.push('Небходимо ввести firstName')
			return errors
		},
		lastNameErrors: function() {
			const errors = []	
			if (!this.form.lastName.isDirty) return errors
			if (!this.form.lastName.value) errors.push('Небходимо ввести lastName')
			return errors
		},
		emailErrors: function() {
			const errors = []	
			if (!this.form.email.isDirty) return errors
			if (!this.form.email.value) errors.push('Небходимо ввести email')
			if (this.form.email.value.search(/.+@.+\..+/)) errors.push('Email должен соответствовать email-маске')
			return errors
		},
		phoneErrors: function() {
			const errors = []	
			if (!this.form.phone.isDirty) return errors
			if (!this.form.phone.value) errors.push('Небходимо ввести phone')
			if (this.form.phone.value.search(/\(\d{3}\)\d{3}-\d{4}$/)) errors.push('Phone должен соответствовать маске (xxx)xxx-xxxx')
			return errors
		},
	},

	methods: {
		submit() {
			this.touchForm()
			
			if (this.formHasErrors) return
			
			const user = Object.fromEntries(
				Object.entries(this.form).map(([key, field]) => (
						[key, field.value]
					)
				)
			)

			this.$emit('submit', user) 
			this.clearForm()
		},
		cancel() {
			this.clearForm()
			this.$emit('cancel')
		},
		touchForm() {
			Object.values(this.form).forEach(field => field.isDirty = true)
		},
		touch(field) {
			field.isDirty = true
		},
		clearForm() {
			Object.values(this.form).forEach(field => { 
				field.isDirty = false
				field.value = ''
			})
		},
	},
}
</script>