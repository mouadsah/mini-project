<template>
	<div>
		<input class="inp-cbx" :id="id" type="checkbox" style="display: none" :value="val" v-model="checked" @change="onChange" :required="required" :disabled="disabled" />
		<label class="label-checkbox" :for="id">
			<span>
				<svg width="12px" height="10px" viewbox="0 0 12 10">
					<polyline points="1.2 6 4.5 10 9 2"></polyline><!-- 1.5 6 4.5 9 10.5 2 -->
				</svg>
			</span>
			<span :class="className" v-if="label != ''">{{ label }}</span>
		</label>
	</div>
</template>
<script>
	export default {
		name: 'CheckBox',
		props: {
			label	: {type: String, default:''}, /* , required: true */
			id	 	: {type: String, default:'customCheckbox'},
			// checkBox: {type: String, default:''},
			'value' : { },
			'val'	: { },
			required	: {type: Boolean, default: false },
			disabled	: {type: Boolean, default: false },
			className	: {type: String, default: '' }
		},
		mounted() {
			
		},
		data() {
			return {
				checkedProxy: false
			}
		},
		computed: {
			checked: {
				get () {
					return this.value
				},
				set (val) {
					this.checkedProxy = val
				}
			}
		},
		methods: {
			onChange: function(/* e */) {
				this.$emit('input', this.checkedProxy)
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
