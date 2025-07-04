<template>
	<slot v-if="meta.slot" :name="column.name">
		<span>
			{{ modelValue }}
		</span>
	</slot>
	<span :class="cssClass" @click="meta.clickable ? $emit('clicked') : null" v-else>
		<fa :icon="modelValue ? 'check' : 'times'" size="sm" v-if="meta.boolean" />
		<fa :icon="modelValue" v-else-if="meta.icon && modelValue" />
		<template v-else-if="column.enum">
			{{ column.enum._get(modelValue) }}
		</template>
		<template v-else-if="meta.translatable">
			{{ i18n(modelValue) }}
		</template>
		<template v-else>{{ modelValue }}</template>
	</span>
</template>

<script>
import { FontAwesomeIcon as Fa } from '@fortawesome/vue-fontawesome';

export default {
	name: 'TableCell',

	components: { Fa },

	inject: ['i18n'],

	props: {
		column: {
			required: true,
			type: Object,
		},
		hiddenControls: {
			default: false,
			type: Boolean,
		},
		modelValue: {
			required: true,
			type: null,
		},
	},

	emits: ['clicked'],

	computed: {
		cssClass() {
			return [this.boolean, this.clickable, this.icon]
				.filter(v => v).join(' ') + ' ' + this.sanitizeVariableName(this.modelValue);
		},
		boolean() {
			return this.meta.boolean && !this.meta.slot
				? ['tag is-table-tag icon', this.modelValue ? 'is-success' : 'is-danger']
					.join(' ')
				: null;
		},
		clickable() {
			return this.meta.clickable
				? 'is-clickable has-text-info'
				: null;
		},
		icon() {
			return this.meta.icon
				? 'icon is-small'
				: null;
		},
		meta() {
			return this.column.meta;
		},

	},
	methods: {
			sanitizeVariableName(str) {
				return str
					.normalize("NFD")
					.replace(/[\u0300-\u036f]/g, "")
					.replace(/\s+/g, "_")
					.toLowerCase()
			}
		},
};
</script>

<style lang="scss">
.is-clickable {
	cursor: pointer;
}

.tag.is-table-tag {
	padding: 0 5px;
	font-size: 0.8rem;
	font-weight: bold;
	height: 1.4em;
	-webkit-box-shadow: 0 1px 1px rgba(10, 10, 10, 0.2);
	box-shadow: 0 1px 1px rgba(10, 10, 10, 0.2);
}
</style>
