<template>
	<div class="container mt-5">
		<!-- Main button -->
		<div class="row">
			<div class="col-6 mb-4">
				<button :class="['btn text-capitalize mr-2', checkMode == 'create' ? 'btn-primary' : 'btn-success']" @click="saveAction(checkMode)">{{ checkMode == 'create' ? checkMode : 'validate' }}</button>
				<button class="btn btn-outline-secondary" @click="saveAction('cancel')" v-if="checkMode != 'create'">Cancel</button>
			</div>
		</div>
		<!-- ToDo List -->
		<div class="row">
			<div class="col-6">
				<!-- ToDo Active -->
				<div class="card card-items">
					<div class="card-header d-flex justify-content-between bg-white">
						<h6 class="my-auto">ToDo Active</h6>
						<span>{{ countElements }} {{ countElements == 1 ? 'item' : 'items' }}</span>
					</div>
					<div class="card-body doto-items">
						<template v-for="(item, id) in ToDoElements">
							<Element :key="`active-${id}`" :item="item" @itemAction="itemAction" v-if="item.status != 'terminated'" :disableAction="checkMode != 'create'" :currentItem="formElement" />
						</template>
					</div>
				</div>
			</div>
			<div class="col-6">
				<!-- ToDo Terminated -->
				<div class="card card-items">
					<div class="card-header d-flex justify-content-between bg-white">
						<h6 class="my-auto">ToDo Terminated</h6>
						<span>{{ countTerminated }} {{ countTerminated == 1 ? 'item' : 'items' }}</span>
					</div>
					<div class="card-body doto-items">
						<template v-for="(item, id) in ToDoElements">
							<Element :key="`terminated-${id}`" :item="item" @itemAction="itemAction" v-if="item.status == 'terminated'" :disableAction="checkMode != 'create'" :currentItem="formElement" />
						</template>
					</div>
				</div>
			</div>
		</div>
		
		<!-- Modal Actions (Add, Edit) -->
		<modal v-show="toggle.actions" @close="callAction('cancel'), checkMode = 'create'" size="md" title>
			<template v-slot:body>
				<div>
					<div class="form-group">
						<check-box id="asdasd" v-model="terminated" val="terminated" label="Terminated" :disabled="checkMode == 'create'"></check-box>
					</div>
					<div class="form-group">
						<label for="title">Title</label>
						<input type="text" id="title" :class="['form-control', emptyFeild ? 'border-danger' : '']" placeholder="Title" v-model.trim="formElement.title" @keyup="checkFeild" />
						<small class="text-danger" v-if="emptyFeild">*Le titre doit être unique et non vide !</small>
					</div>
					<div class="form-group">
						<label for="description">Description</label>
						<textarea class="form-control" id="description" placeholder="description" v-model="formElement.description" rows="4"></textarea>
					</div>
				</div>
			</template>
			<template v-slot:footer>
				<div>
					<button class="btn btn-outline-secondary mr-3" @click="callAction('cancel'), checkMode = 'create'">Cancel</button>
					<button class="btn btn-primary" @click="callAction(checkMode == 'create' ? 'add' : checkMode)"><i :class="['fa', checkMode == 'create' ? 'fa-plus' : 'fa-edit']"></i> {{ checkMode == 'create' ? 'Add' : 'Edit' }}</button>
				</div>
			</template>
		</modal>
		
		<!-- Modal Confirm Remove -->
		<modal size="confirm" v-show="toggle.remove" hide-header hide-footer>
			<template v-slot:body>
				<div class="text-center">
					<div>
						<svg xmlns="http://www.w3.org/2000/svg" style="-ms-transform: rotate(360deg); -webkit-transform: rotate(360deg); transform: rotate(360deg);width:6em; height:6em;" viewBox="0 0 512 512">
							<path d="M448 256c0-106-86-192-192-192S64 150 64 256s86 192 192 192s192-86 192-192z" fill="none" stroke="#ea1c0d" stroke-miterlimit="10" stroke-width="32"/>
							<path fill="none" stroke="#ea1c0d" stroke-linecap="round" stroke-linejoin="round" stroke-width="32" d="M320 320L192 192"/>
							<path fill="none" stroke="#ea1c0d" stroke-linecap="round" stroke-linejoin="round" stroke-width="32" d="M192 320l128-128"/>
						</svg>
					</div>
					<div>
						<h4>Are you sure !</h4>
						<p class="m-0" style="font-size: 13px;">Do you want really to delete this note ?</p>
						<h6>{{  formElement.title }}</h6>
					</div>
					<div>
						<button class="btn btn-secondary mr-3" @click="saveAction('cancel')">Cancel</button>
						<button class="btn btn-danger" @click="confirmRemove">
							<i class="fa fa-trash"></i> <span>Confirm</span>
						</button>
					</div>
				</div>
			</template>
		</modal>
		
	</div>
</template>

<script>
import {Modal, CheckBox, Element} from '@/components'

export default {
	name: 'ToDoList',
	data() {
		return {
			formElement		: { id: "", title: "", description: "", status: "" },
			
			ToDoElements	: [
				{id: 1, title: "test 2", description: "test 2", status: 'active'}, // active - draft - terminated
				{id: 2, title: "test 1", description: "test 1", status: 'active'},
			],
			
			terminated		: false,
			emptyFeild		: false,
			
			checkMode		: 'create', // create - validate - removeItem - editItem
			toggle			: { actions: false, remove: false },
		};
	},
	
	components: {
		Modal, CheckBox, Element
	},
	
	methods: {
		callAction(action = '') {
			const that = this
			if( ['add', 'cancel'].includes(action) ) {
				that[`${action}Action`]()
				return
			}
			that[that.checkMode]() // that.checkMode == 'editItem'
		},
		cancelAction() {
			const that 			= this
			that.toggle.actions = false
			Object.keys(that.formElement).forEach( (item) => {
				that.formElement[item] = ""
			} )
			that.emptyFeild 	= false
			that.terminated 	= false
		},
		addAction() {
			const that = this
			
			let checkTitle 	= that.ToDoElements.filter(item => that.formElement.title.trim().toLowerCase() == item.title.trim().toLowerCase())
			if( checkTitle.length > 0 || that.formElement.title.trim() == '' ) {
				that.emptyFeild = true
				return
			}
			that.emptyFeild = false
			
			that.toggle.actions = false
			that.checkMode = 'validate'
			
			that.ToDoElements.unshift({...that.formElement, status: 'draft', id: (new Date()).getTime()})
			that.cancelAction()
		},
		
		// ---
		itemAction({action, item}) {
			const that 			= this
			that.terminated		= item.status == "terminated"
			that.checkMode		= `${action}Item`
			that.formElement 	= {...item}
			that.toggle.actions = action == 'edit' // editItem
		},
		editItem() {
			const that = this
			
			// Check if the title is unique and defined
			let checkTitle 	= that.ToDoElements.filter(item => (that.formElement.title.trim().toLowerCase() == item.title.trim().toLowerCase() && that.formElement.id != item.id))
			if( checkTitle.length > 0 || that.formElement.title.trim() == '' ) {
				that.emptyFeild = true
				return
			}
			that.emptyFeild 	= false
			
			that.formElement.status = 'edit'
			that.toggle.actions 	= false
		},
		confirmRemove() {
			const that = this
			that.ToDoElements 	= that.ToDoElements.filter(item => that.formElement.id != item.id)
			that.checkMode		= 'create'
			that.cancelAction()
			that.toggle.remove	= false
		},
		// ---
		saveAction(action) {
			const that = this
			if( ['validate', 'cancel'].includes(action) ) {
				that[`${action}Save`]()
				return
			} else if( that.checkMode == 'removeItem' ) { // that[that.checkMode]()
				that.toggle.remove = true
				return
			} else if( that.checkMode == 'editItem' && that.formElement.status == 'edit' ) {
				that.editAction()
				return
			}
			
			that.toggle.actions = true
		},
		editAction() {
			const that = this
			for( let item of that.ToDoElements ) {
				if( that.formElement.id == item.id ) {
					item.title		 = that.formElement.title
					item.description = that.formElement.description
					item.status		 = that.terminated ? 'terminated' : 'active' // item.status
				}
			}
			that.toggle.actions = false
			that.checkMode		= 'create'
			that.terminated		= false
			that.cancelAction()
			
			// Request Api
			// endPoint : https://jsonplaceholder.typicode.com/
			// params 	: that.formElement
			// console.log(that.formElement)
		},
		cancelSave() {
			const that 		= this
			that.checkMode	= 'create'
			that.ToDoElements = that.ToDoElements.filter(item => item.status === 'active')
			that.cancelAction()
		},
		validateSave() {
			const that 		= this   
			// let element		= {}
			that.checkMode	= 'create'
			that.ToDoElements.forEach( (item) => {
				// if (item.status == 'draft') element = item
				item.status = item.status == 'draft' ? 'active' : item.status
			} )
			// Request Api
			// endPoint : https://jsonplaceholder.typicode.com/
			// params 	: element
			// console.log(element)
		},
		checkFeild() {
			const that 	= this
			if( that.emptyFeild && that.formElement.trim != '' ) {
				that.emptyFeild = false
			}
		}
	},
	computed: {
		countElements() {
			const that 	 = this
			let elements = that.ToDoElements.filter(item => item.status != "terminated")
			return elements.length
		},
		countTerminated() {
			const that 	 = this
			let elements = that.ToDoElements.filter(item => item.status === "terminated")
			return elements.length
		},
	},
}
</script>

<style scoped lang="scss">
	.card-items {
		box-shadow: 0px 0px 6px #ddd;
		.doto-items {
			max-height: 350px;
			overflow-y: auto;
			
			div:hover {
				box-shadow: 0px 0px 4px #ccc;
			}
			
			.item-active {
				background-color: #eee;
			}
		}
	}
	
	.btn {
		min-width: 90px !important;
	}
</style>
