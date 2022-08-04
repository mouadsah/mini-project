<template>
	<div :class="['border p-2 mb-2 d-flex justify-content-between', item.status != 'draft' ? 'item-active' : '']" v-if="item.status != 'terminated'">
		<div class="my-auto">
			{{ item.title }}
		</div>
		<div v-if="item.status != 'draft'">
			<button class="btn btn-sm btn-default" @click="itemAction('edit', item)"><i class="fa fa-edit"></i></button>
			<button class="btn btn-sm btn-default ml-2" @click="itemAction('remove', item)"><i class="fa fa-close"></i></button>
		</div>
	</div>
</template>
<script>
	export default {
		name: 'Element',
		props: {
			item	: {type: Object},
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
			itemAction: (action, item) => {
				this.$emit('itemAction', action, item)
			}
		}
	}
</script>
<style lang="scss">
	
	.label-checkbox {
		margin: auto;
		-webkit-user-select: none;
		user-select: none;
		cursor: pointer;
		
		span {
			display: inline-block;
			vertical-align: middle;
			transform: translate3d(0, 0, 0);
			
			&:first-child {
				position: relative;
				left: 5px;
				width: 17px;
				height: 17px;
				border-radius: 3px;
				transform: scale(1);
				vertical-align: middle;
				border: 1px solid #9098A9;
				transition: all 0.2s ease;
				background-color: #fff;
				
				svg {
					position: absolute;
					top: 2px;
					left: 2px;
					fill: none;
					stroke: #ffffff;
					stroke-width: 2;
					stroke-linecap: round;
					stroke-linejoin: round;
					stroke-dasharray: 16px;
					stroke-dashoffset: 16px;
					transition: all 0.3s ease;
					transition-delay: 0.1s;
					transform: translate3d(0, 0, 0);
				}
			
				&:before {
					content: "";
					width: auto; /* 100% */
					height: auto; /* 100% */
					background: #2196f3;
					display: block;
					transform: scale(0);
					opacity: 1;
					border-radius: 50%;
				}
			}
			
			&:last-child {
				padding-left: 8px;
				/* margin-left: 2px; */
			}
		}
		
		&:hover span:first-child {
			border-color: #2196f3;
		}
		
	}

	.inp-cbx:checked + .label-checkbox span:first-child {
		background: #2196f3;
		border-color: #2196f3;
	}
	.inp-cbx:checked + .label-checkbox span:first-child svg {
		stroke-dashoffset: 0;
	}
	.inp-cbx:checked + .label-checkbox span:first-child:before {
		transform: scale(3.5);
		opacity: 0;
	}
	
	
	.inp-cbx:disabled + .label-checkbox span:first-child {
		background-color: #ccc;
		border-color: #9098A9;
	}
	.inp-cbx:disabled + .label-checkbox {
		cursor: not-allowed !important;
	}
	
</style>
