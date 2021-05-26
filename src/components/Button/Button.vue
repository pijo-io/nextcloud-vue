<!--
 - @copyright Copyright (c) 2020 Marco Ambrosini <marcoambrosini@pm.me>
 -
 - @author Marco Ambrosini <marcoambrosini@pm.me>
 -
 - @license GNU AGPL version 3 or any later version
 -
 - This program is free software: you can redistribute it and/or modify
 - it under the terms of the GNU Affero General Public License as
 - published by the Free Software Foundation, either version 3 of the
 - License, or (at your option) any later version.
 -
 - This program is distributed in the hope that it will be useful,
 - but WITHOUT ANY WARRANTY; without even the implied warranty of
 - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 - GNU Affero General Public License for more details.
 -
 - You should have received a copy of the GNU Affero General Public License
 - along with this program. If not, see <http://www.gnu.org/licenses/>.
 -
 -->

<docs>

### General description

The button

### Usage

</docs>

<template>
	<button v-ripple="'var(--color-ripple)'"
		class="nc-button button-vue"
		v-bind="$attrs"
		:class="buttonClassObject"
		@click="handleClick"
		@blur="handleBlur"
		@keyup.tab.exact="handleTabUp">
		<div class="nc-button__wrapper">
			<div v-if="hasIcon" class="nc-button__icon">
				<slot />
			</div>
			<span v-if="hasText" class="nc-button__text">{{ text }}</span>
		</div>
	</button>
</template>
<script>
import ripple from 'vue-ripple-directive'

export default {
	name: 'Button',

	directives: {
		ripple,
	},

	props: {
		/**
		 * The text of the button
		 */
		text: {
			type: String,
			default: '',
		},

		/**
		 * Specifies the button color
		 * Accepted values: primary, error, warning, success.
		 */
		color: {
			type: String,
			validator(value) {
				return ['primary', 'error', 'warning', 'success', ''].indexOf(value) !== -1
			},
			default: '',
		},

		/**
		 * Specifies whether the button should span all the available width
		 */
		wide: {
			type: Boolean,
			default: false,
		},
	},

	data() {
		return {
			/**
			 * Keeps track of whether the element's focus status is due to having
			 * tabbed to it. We use this to display a thick 'focus outline' only
			 * when the user is navigating with the keyboard.
			 */
			tabbed: false,
		}
	},

	computed: {
		hasText() {
			return this.text
		},

		hasIcon() {
			return this.$slots.default !== undefined
		},

		// Classes applied to the button element
		buttonClassObject() {
			return {
				
				'nc-button': !this.iconOnly,
				// If icon only, some additional css rules are required
				'nc-button--icon-only': this.iconOnly,
				[this.color]: this.color !== '',
				wide: this.wide,
				tabbed: this.tabbed,
			}

		},

		iconOnly() {
			return this.hasIcon && !this.hasText
		}
	},

	methods: {
		// Forward the click event to the parent component
		handleClick(event) {
			this.$emit('click', event)
		},

		handleTabUp() {
			this.tabbed = true
		},

		handleBlur() {
			this.tabbed = false
		},
	},
}

</script>

<style lang="scss" scoped>

.nc-button {
	position: relative;
	overflow: hidden;
	border: 0;
	font-size: var(--default-font-size);
	height: $clickable-area;
	min-width: $clickable-area;
	display: flex;
	align-items: center;
	justify-content: center;
	border-radius: var(--border-radius-pill);
	transition: background-color 0.1s linear !important;
	transition: border 0.1s linear;
	// No outline feedback for focus. Handled with a toggled class in js (see data)
	&:focus {
		outline: none;
	}
	// No active state for buttons
	&:active {
		background-color: unset;
	}

	&__wrapper {
		padding: 0 4px;
		display: inline-flex;
		align-items: center;
		justify-content: space-around;
	}

	&__icon {
		height: 16px;
		width: 16px;
		margin-right: 8px;
	}
	&__text {
		font-weight: bold;
	}

	// ## Types of button

	// ### Icon-only button
	&--icon-only {
		width: $clickable-area;
		.nc-button__wrapper {
			padding: 0;
		}

		.nc-button__icon {
			margin-right: 0;
		}
	}

	// ### Contained button
	&--contained {
		// 2 variants, regular and primary
		// regular:
		color: #0082C9;
		background-color: #E1F4FF;
		border-color: #E1F4FF;
		&:hover {
			color: #005381;
			background-color: #C9ECFF;
			border-color:s #C9ECFF;
		}
		// Variants
		&.primary {
			background-color: var(--color-primary-element);
			border-color: var(--color-primary-element);
			color: var(--color-primary-text);
			background-image: linear-gradient(40deg, #0082c9 0%, #1699e1 100%);
			&:hover {
				background-color: var(--color-primary);
				border-color: var(--color-primary);
				background-image: linear-gradient(40deg, #0277b6 0%, #1592d4 100%);
			}
		}
	}

	// Wide button spans the whole width of the container
	&.wide {
		width: 100%;
	}

	// We use around our buttons instead of an outline, so that the added "border"
	// coincides with the border of the element.
	&.tabbed {
		box-shadow: 0px 0px 0px 2px var(--color-main-text);
	}
}
</style>
