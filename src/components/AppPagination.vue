<template>
	<div>
		<!-- previous pageNumber btn -->
		<v-btn 
			class="mr-1"
			:disabled="page === 1"
			@click="prevPage()"
		>
			<v-icon>
				mdi-menu-left
			</v-icon>
		</v-btn>

		<template v-if="normalizedLength > 5">
			<!-- left numbered btns -->
			<span
				v-for="btnNumber in sideRange"
				:key="'left' + btnNumber"
			>
				<v-btn
					v-if="isLeftBtnActive(btnNumber)"
					class="mx-1"
					:class="{'primary': btnNumber === page}"
					@click="select(btnNumber)"
				>
					{{btnNumber}}
				</v-btn>
			</span>

			<!-- left divider -->
			<span
				v-if="!isInLeftSide(page)"
				class="mx-1 px-2"			
			>
				...
			</span>

			<!-- center btns -->
			<span
				v-for="btnNumber in 2*pageRange + 1"
				:key="'center' + getPageNumber(btnNumber, 'center')"
			>
				<v-btn
					v-if="!isInAnySideRange(page) ^ 
								(isOnLeftBorder(page) || isOnRightBorder(page))"
					class="mx-1"
					:class="{'primary': getPageNumber(btnNumber, 'center') === page}"
					@click="select(getPageNumber(btnNumber, 'center'))"
				>
					{{getPageNumber(btnNumber, 'center')}}
				</v-btn>
			</span>

			<!-- right divider -->
			<span
				v-if="!isInRightSide(page)"
				class="mx-1 px-2"			
			>
				...
			</span>
			
			<!-- right numbered btns -->
			<span
				v-for="btnNumber in sideRange"
				:key="'right' + getPageNumber(btnNumber, 'right')"
			>
				<v-btn
					v-if="isRightBtnActive(getPageNumber(btnNumber, 'right'))"
					class="mx-1"
					:class="{'primary': getPageNumber(btnNumber,'right') === page}"
					@click="select(getPageNumber(btnNumber, 'right'))"
				>
					{{getPageNumber(btnNumber, 'right')}}
				</v-btn>
			</span>
		</template>
		<template v-else>
			<v-btn
					v-for="pageNumber in normalizedLength"
					:key="pageNumber"
					class="mx-1"
					:class="{'primary': pageNumber === page}"
					@click="select(pageNumber)"
				>
					{{pageNumber}}
				</v-btn>
		</template>

		<!-- next pageNumber btn -->
		<v-btn
			class="mx-1"
			:disabled="page === normalizedLength"
			@click="nextPage()"
		>
			<v-icon>
				mdi-menu-right
			</v-icon>
		</v-btn>
	</div>
</template>

<script>
export default {
	name: 'AppPagination',

	props: {
		length: {
			type: Number,
			required: true,
		},
		totalVisible: {
			type: Number,
			default: 7,
		},
		sideRange: {
			type: Number,
			default: 3,
		},
		pageRange: {
			type: Number,
			default: 1,
		},
		page: {
			type: Number,
			default: 1,
		},
	},

	computed: {
		normalizedLength: function() {
			return this.length >= 1 ? this.length : 1
		},
	},

	methods: {
		select(pageNumber) {
			this.$emit('change', pageNumber)
		},
		prevPage() {
			if (this.page > 1)
				this.$emit('change', this.page - 1)
		},
		nextPage() {
			if (this.page < this.normalizedLength)
				this.$emit('change', this.page + 1)
		},
		/**
		 * [Returns the pageNumber number from button number depending on the side the button is on]
		 * @param  {[Number]} btnNumber [button number]
		 * @param  {[String]} side [side of the button: "center" or "right"]
		 * @return {[Number]}      [pageNumber number or btn number if the side is invalid]
		 */
		getPageNumber(btnNumber, side) {
			if (side === "right")
				return this.normalizedLength + btnNumber - this.sideRange
			
			if (side === "center") {
				if (this.isOnLeftBorder(this.page))
					return this.page + btnNumber - this.pageRange

				if (this.isOnRightBorder(this.page))
					return this.page + btnNumber - this.pageRange - 2
				
				return this.page + btnNumber - this.pageRange - 1
			}
			
			return btnNumber
		},
		isInLeftSide: function(pageNumber) {
			return pageNumber <= this.sideRange
		},
		isInRightSide: function(pageNumber) {
			return this.normalizedLength - pageNumber < this.sideRange
		},
		isInAnySideRange(pageNumber) {
			return this.isInLeftSide(pageNumber) || this.isInRightSide(pageNumber)
		},
		isOnLeftBorder: function(pageNumber) {
			return pageNumber === this.sideRange
		},
		isOnRightBorder: function(pageNumber) {
			return this.normalizedLength - pageNumber === this.sideRange - 1
		},

		isLeftBtnActive(pageNumber) {
			// the first button will always be displayed
			return pageNumber === 1 || 
						(this.isInLeftSide(this.page) && 
						(!this.isOnLeftBorder(pageNumber) || this.page != pageNumber)) ||
						(this.isInRightSide(this.page) && !this.isOnRightBorder(this.page))
		},
		isRightBtnActive(pageNumber) {
			// the last button will always be displayed
			return pageNumber === this.normalizedLength || 
						(this.isInRightSide(this.page) &&
						(!this.isOnRightBorder(pageNumber) || this.page != pageNumber)) ||
						(this.isInLeftSide(this.page) && !this.isOnLeftBorder(this.page))
		},
	},

	watch: {
    length() {
			this.$emit('change', 1)
    },
  },
}
</script>