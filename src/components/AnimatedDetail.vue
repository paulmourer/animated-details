<template>
	<div
		class="container"
		:style="{ '--height': finalHeight ? finalHeight + 'px' : 'unset', '--time': time + 's' }"
	>
		<details ref="details" :class="{ measuring }" id="detail">
			<summary>{{ title }}</summary>
			<div>{{ content }}</div>
		</details>
	</div>
</template>

<script>
// eslint-disable-next-line
let MEASURING_TIMEOUT = null
export default {
	mounted() {
		this.setDetailsHeight()
		window.addEventListener('resize', this.debouncedSetDetailsHeight)
	},
	beforeUnmount() {
		clearTimeout(MEASURING_TIMEOUT)
		window.removeEventListener('resize', this.debouncedSetDetailsHeight)
	},
	methods: {
		debouncedSetDetailsHeight() {
			const self = this
			if (MEASURING_TIMEOUT) clearTimeout(MEASURING_TIMEOUT)
			MEASURING_TIMEOUT = setTimeout(() => {
				self.setDetailsHeight.apply(self)
			}, 1000)
		},
		setDetailsHeight() {
			this.measuring = true
			this.$refs.details.open = true
			this.$nextTick(function () {
				this.finalHeight = this.$refs.details.getBoundingClientRect().height
				this.$refs.details.open = false
				this.measuring = false
			})
		},
	},
	data() {
		return {
			title: 'This is the title',
			content: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.',
			PX_PER_SEC: 300,
			measuring: false,
			finalHeight: 0
		}
	},
	computed: {
		time() {
			return this.finalHeight / this.PX_PER_SEC || 0
		}
	},
}
</script>

<style scoped>
@keyframes show {
	0% {
		opacity: 0;
	}
	100% {
		opacity: 1;
	}
}
.container {
	width: 80%;
	margin: auto;
}
details {
	overflow: hidden;
	transition: height var(--time) cubic-bezier(0.16, 1, 0.3, 1);
}
details {
	height: 24px;
}
details[open] {
	height: var(--height);
}
details > div {
	opacity: 0;
	padding: 1rem;
}
details[open] > div {
	animation: show calc(var(--time) * 0.6) ease-in-out forwards;
	animation-delay: calc(var(--time) * 0.3);
}
details.measuring {
	transition: height 0s;
	height: unset;
}
</style>