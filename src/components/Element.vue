<template>
	<div :class="['border p-2 mb-2 d-flex justify-content-between', item.status != 'draft' ? 'item-active' : '']">
		<p :class="['my-auto', currentItem.id == item.id ? 'font-weight-bold' : '' ]">
			{{ item.title }}
		</p>
		<div v-if="item.status != 'draft'">
			<button class="btn btn-sm btn-default" @click="itemAction('edit', item)" :disabled="disableAction"><i class="fa fa-edit text-primary"></i></button>
			<button class="btn btn-sm btn-default ml-2" @click="itemAction('remove', item)" :disabled="disableAction"><i class="fa fa-close text-danger"></i></button>
		</div>
	</div>
</template>
<script>
	export default {
		name: 'Element',
		props: {
			disableAction	: {type: Boolean, default: false},
			currentItem		: {type: Object},
			item			: {type: Object},
		},
		mounted() {
			
		},
		data() {
			return {
				
			}
		},
		computed: {
			
		},
		methods: {
			itemAction: function (action, item) {
				const that = this
				if( !that.disableAction ) {
					this.$emit('itemAction', {action, item})
				}
			}
		}
	}
</script>
<style lang="scss">
	.btn.disabled, .btn:disabled {
		cursor: not-allowed;
	}
</style>
