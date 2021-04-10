<template>
	<div>
		<!-- previous page btn -->
		<v-btn 
			class="mr-1"
			:disabled="currentPage === 1"
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
					:class="{'primary': btnNumber === currentPage}"
					@click="select(btnNumber)"
				>
					{{btnNumber}}
				</v-btn>
			</span>

			<!-- left divider -->
			<span
				v-if="!isInLeftSide(currentPage)"
				class="mx-1 px-2"			
			>
				...
			</span>

			<!-- center btns -->
			<span
				v-for="btnNumber in 2*currentPageRange + 1"
				:key="'center' + getPageNumber(btnNumber, 'center')"
			>
				<v-btn
					v-if="!isInAnySideRange(currentPage) ^ 
								(isOnLeftBorder(currentPage) || isOnRightBorder(currentPage))"
					class="mx-1"
					:class="{'primary': getPageNumber(btnNumber, 'center') === currentPage}"
					@click="select(getPageNumber(btnNumber, 'center'))"
				>
					{{getPageNumber(btnNumber, 'center')}}
				</v-btn>
			</span>

			<!-- right divider -->
			<span
				v-if="!isInRightSide(currentPage)"
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
					:class="{'primary': getPageNumber(btnNumber,'right') === currentPage}"
					@click="select(getPageNumber(btnNumber, 'right'))"
				>
					{{getPageNumber(btnNumber, 'right')}}
				</v-btn>
			</span>
		</template>
		<template v-else>
			<v-btn
					v-for="page in normalizedLength"
					:key="page"
					class="mx-1"
					:class="{'primary': page === currentPage}"
					@click="select(page)"
				>
					{{page}}
				</v-btn>
		</template>

		<!-- next page btn -->
		<v-btn
			class="mx-1"
			:disabled="currentPage === normalizedLength"
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
	
	data: () => ({
		currentPage: 1,
	}),

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
		currentPageRange: {
			type: Number,
			default: 1,
		},
		page: {
			type: Number,
		},
	},

	computed: {
		normalizedLength: function() {
			return this.length >= 1 ? this.length : 1
		},
	},

	methods: {
		select(page) {
			this.currentPage = page
		},
		prevPage() {
			if (this.currentPage > 1) this.currentPage--
		},
		nextPage() {
			if (this.currentPage < this.normalizedLength) this.currentPage++
		},
		/**
		 * [Returns the page number from button number depending on the side the button is on]
		 * @param  {[Number]} btnNumber [button number]
		 * @param  {[String]} side [side of the button: "center" or "right"]
		 * @return {[Number]}      [page number or btn number if the side is invalid]
		 */
		getPageNumber(btnNumber, side) {
			if (side === "right")
				return this.normalizedLength + btnNumber - this.sideRange
			
			if (side === "center") {
				if (this.isOnLeftBorder(this.currentPage))
					return this.currentPage + btnNumber - this.currentPageRange

				if (this.isOnRightBorder(this.currentPage))
					return this.currentPage + btnNumber - this.currentPageRange - 2
				
				return this.currentPage + btnNumber - this.currentPageRange - 1
			}
			
			return btnNumber
		},
		isInLeftSide: function(page) {
			return page <= this.sideRange
		},
		isInRightSide: function(page) {
			return this.normalizedLength - page < this.sideRange
		},
		isInAnySideRange(page) {
			return this.isInLeftSide(page) || this.isInRightSide(page)
		},
		isOnLeftBorder: function(page) {
			return page === this.sideRange
		},
		isOnRightBorder: function(page) {
			return this.normalizedLength - page === this.sideRange - 1
		},

		isLeftBtnActive(page) {
			// the first button will always be displayed
			return page === 1 || 
						(this.isInLeftSide(this.currentPage) && 
						(!this.isOnLeftBorder(page) || this.currentPage != page)) ||
						(this.isInRightSide(this.currentPage) && !this.isOnRightBorder(this.currentPage))
		},
		isRightBtnActive(page) {
			// the last button will always be displayed
			return page === this.normalizedLength || 
						(this.isInRightSide(this.currentPage) &&
						(!this.isOnRightBorder(page) || this.currentPage != page)) ||
						(this.isInLeftSide(this.currentPage) && !this.isOnLeftBorder(this.currentPage))
		},
	},

	watch: {
		currentPage() {
			this.$emit('change', this.currentPage)
    },

    page() {
			this.currentPage = this.page
    },
  },
}
</script>