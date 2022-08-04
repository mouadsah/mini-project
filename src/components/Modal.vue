<template>
	
	<transition name="modal-fade">
		<div class="modal-backdrop-md" role="dialog">
			<div :class="['modal-md', 'modal-md-' + size, size == 'max' ? 'modal-height-max' : '']" ref="modal">
				<header v-if="!hideHeader">
					<slot name="header">
						<div class="modal-header-md d-flex">
							<slot name="title">
								<span>{{ title }} <span v-if="Loading" class="fs-14 text-primary"><i class="fa fa-cog fa-spin"></i> Loading...</span></span>
							</slot>
							<button type="button" class="btn-close btn-right" @click="close" aria-label="Close modal">&times;</button>
						</div>
					</slot>
				</header>
				<section :class="['modal-body-md', scrollHide ? '' : 'overflow-auto', bodyClass, 'dialog-' + size]">
					<slot name="body">
						I'm the default body!
					</slot>
				</section>
				<footer class="modal-footer-md text-right" v-if="!hideFooter">
					<slot name="footer">
						<button type="button" class="btn btn-green" @click="close" aria-label="Close modal">Close</button>
					</slot>
				</footer>
			</div>
		</div>
	</transition>
	
</template>
<script>
	export default {
		props: {
			title 	  : { type : String, default : "Modal" 	 },
			size	  : { type : String, default : "md" 	 },
			hideHeader: { type : Boolean,default : false 	 },
			hideFooter: { type : Boolean,default : false 	 },
			scrollHide: { type : Boolean,default : false 	 },
			Loading	  : { type : Boolean,default : false 	 },
			bodyClass : { type : String, default : '' 		 },
		},
		data() {
			return {
				
			};
		},
		methods: {
			close() { // event
				this.$emit('close');
			},
		},
		mounted() {
			
		}
	}
</script>
<style lang="scss">
	
	.modal-backdrop-md {
		position: fixed;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: rgba(0, 0, 0, 0.3);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 9999;
		
		.modal-md {
			border-radius: 8px;
			background: #ffffff;
			box-shadow: 2px 2px 20px 1px;
			overflow: initial;
			display: flex;
			flex-direction: column;
			max-height: 96vh;
		}
		
		.modal-height-max {
			height: calc(100vh - 15px);
			>.modal-body-md {
				height: 100vh;
				max-height: 100vh !important;
			}
		}
		
		.modal-md-xs { max-width: 245px; min-width: 20%; }
		.modal-md-confirm { max-width: 340px; min-width: 25%; }
		.modal-md-sm { max-width: 545px; min-width: 40%; }
		.modal-md-md { max-width: 745px; min-width: 60%; }
		
		.modal-md-lg {
			max-width: 1315px;
			min-width: 90%;
			.dialog-lg {
				min-height: 78vh;
			}
		}
		.modal-md-max { width: calc(100% - 15px); }
		
		.modal-header-md, .modal-footer-md {
			padding: 15px;
			border-radius: 0px 0px 8px 8px;
			button {
				border-radius: 20px;
				width: 45px;
			}
		}
		
		.modal-header-md {
			border-bottom: 1px solid #eeeeee;
			justify-content: space-between;
			font-size: 20px;
		}
		
		.modal-footer-md {
			border-top: 1px solid #eeeeee;
			/* justify-content: flex-end; */
		}
		
		.modal-body-md {
			position: relative;
			padding: 20px 15px;
			min-height: 16vh; /* 32vh */
		}
		
		.btn-close {
			border: none;
			padding: 0px 20px;
			cursor: pointer;
			font-weight: bold;
			background: transparent;
		}
		
		.modal-fade-enter, .modal-fade-leave-active {
			opacity: 0;
		}

		.modal-fade-enter-active, .modal-fade-leave-active {
			transition: opacity 0.5s ease;
		}
	}
</style>