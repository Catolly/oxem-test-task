<template>
	<v-simple-table>
		<thead>
			<tr>
				<th
					v-for="(key, index) of keys"
					:key="key"
				>
					<v-btn 
						class="btn d-inline-flex align-center"
						plain
						@click="sortBy(key);"
					>
						<span class="header grey--text">{{headers[index]}}</span>
						<v-icon 
							:class="{
								'show': sortKey === key,
								'active': sortKey === key && !reverse,
							}"
							size="8" 
							class="ml-2 icon"
						>mdi-triangle</v-icon>
					</v-btn>
				</th>
			</tr>
		</thead>
		<tbody>
			<tr
				v-for="(item, index) of pageSortedItems"
				:key="index"
				:class="{'secondary': item.id === selectedId}"
				class="item"
				@click="select(item.id)"
			>
				<td 
					v-for="(value, key) of item"
					:key="key"
					class="pl-8"
				>{{value}}</td>
			</tr>
		</tbody>
	</v-simple-table>
</template>

<script>
export default {
	name: 'AppTable',
	
	data: () => ({
		sortKey: '',
		reverse: false,
		selectedId: null,
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
		dataPerPage: {
			type: Number,
			default: null,
		},
		page: {
			type: Number,
			default: null,
		}
	},
	
	computed: {
		sortedItems: function() {
			if (!this.sortKey)
				return this.items

			const sortingItems = this.items.slice()
			
			return sortingItems.sort((a, b) => {
				a = a[this.sortKey]
				b = b[this.sortKey]

				return (a === b ? 0 : a < b ? 1 : -1) * (this.reverse ? 1 : -1)
			})
		},

    pageSortedItems: function() {
      const start = (this.page - 1) * this.dataPerPage,
            end = this.page * this.dataPerPage

      return this.sortedItems.slice(start, end)
    },
	},
	
	methods: {
		sortBy(sortKey) {
			this.reverse = (this.sortKey === sortKey) ? !this.reverse : false
			this.sortKey = sortKey
		},
		select(id) {
			this.selectedId = id
		},
	},
	
	watch: {
    selectedId() {
			this.$emit('click', this.selectedId)
    },
    sortKey() {
			this.$emit('reset')
    },
  },

	beforeMount() {
		this.sortKey = this.keys[0]
	},
}
</script>

<style scoped lang="scss">
.header {
	font-size: 1.1em;
}

.btn {
	&:hover .icon {
		visibility: visible;
	}
}

.icon {
	transform: rotate(180deg);
	visibility: hidden;

	&.active {
		transform: rotate(0);
	}
	&.show {
		visibility: visible;
	}
}

.item {
	cursor: pointer;
}
</style>