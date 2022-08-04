<template>
	<div class="container mt-5">
		<div class="row">
			<div class="col-6 mb-4">
				<button class="btn btn-outline-secondary mr-2" @click="saveAction('cancel')" v-if="checkMode != 'create'">Cancel</button>
				<button :class="['btn text-capitalize', checkMode == 'create' ? 'btn-primary' : 'btn-success']" @click="saveAction(checkMode)">{{ checkMode == 'create' ? checkMode : 'validate' }}</button>
			</div>
		</div>
		<div class="row">
			<div class="col-6">
				<div class="card">
					<div class="card-header d-flex justify-content-between bg-white">
						<h6 class="mb-0">ToDo Active</h6>
						<span>{{ countElements }} {{ countElements == 1 ? 'item' : 'items' }}</span>
					</div>
					<div class="card-body doto-items">
						<template v-for="(item) in ToDoElements">
							<Element :key="`item-${item.id}`" :item="item" @itemAction="itemAction" v-if="item.status != 'terminated'" />
						</template>
					</div>
				</div>
			</div>
			<div class="col-6">
				<div class="card">
					<div class="card-header d-flex justify-content-between bg-white">
						<h6 class="mb-0">ToDo Terminated</h6>
						<span>{{ countTerminated }} {{ countTerminated == 1 ? 'item' : 'items' }}</span>
					</div>
					<div class="card-body doto-items">
						<template v-for="(item) in ToDoElements">
							<Element :key="`item-${item.id}`" :item="item" @itemAction="itemAction" v-if="item.status == 'terminated'" />
						</template>
					</div>
				</div>
			</div>
		</div>
		
		<modal v-show="showModal" @close="callAction('cancel'), checkMode = 'create'" size="md" title>
			<template v-slot:body>
				<div>
					<div class="form-group">
						<check-box id="asdasd" v-model="terminated" val="terminated" label="Terminated" :disabled="checkMode == 'create'"></check-box>
					</div>
					<div class="form-group">
						<label for="title">Title</label>
						<input type="text" id="title" :class="['form-control', emptyFeild ? 'border-danger' : '']" placeholder="Title" v-model.trim="formElements.title" @keyup="checkFeild" />
						<small class="text-danger" v-if="emptyFeild">*Le titre doit être unique et non vide !</small>
					</div>
					<div class="form-group">
						<label for="description">Description</label>
						<textarea class="form-control" id="description" placeholder="description" v-model="formElements.description"></textarea>
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
	</div>
</template>

<script>
import {Modal, CheckBox, Element} from '@/components'

export default {
	name: 'ToDoList',
	data() {
		return {
			showModal		: false,
			
			formElements	: { id: "", title: "", description: "", status: "" },
			
			ToDoElements	: [
				{id: 1, title: "test 2", description: "test 2", status: 'active'}, // active - draft - terminated
				{id: 2, title: "test 1", description: "test 1", status: 'active'},
			],
			checkMode		: 'create', // create - validate - removeItem - editItem
			
			terminated		: false,
			
			emptyFeild		: false,
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
			that.showModal 		= false
			Object.keys(that.formElements).forEach( (item) => {
				that.formElements[item] = ""
			} )
			that.emptyFeild 	= false
			that.terminated 	= false
		},
		addAction() {
			const that = this
			
			let checkTitle 	= that.ToDoElements.filter(item => that.formElements.title == item.title)
			if( checkTitle.length > 0 || that.formElements.title.trim() == '' ) {
				that.emptyFeild = true
				return
			}
			that.emptyFeild = false
			
			that.showModal = false
			that.checkMode = 'validate'
			
			that.ToDoElements.unshift({...that.formElements, status: 'draft', id: that.ToDoElements.length + 1})
			that.cancelAction()
		},
		
		// ---
		itemAction({action, item}) {
			const that 			= this
			that.terminated		= false
			that.checkMode		= `${action}Item`
			that.formElements 	= {...item}
			that.showModal 		= action == 'edit' // editItem
		},
		removeItem() {
			const that = this
			that.ToDoElements 	= that.ToDoElements.filter(item => that.formElements.id != item.id)
			that.checkMode		= 'create'
			that.cancelAction()
		},
		editItem() {
			const that = this
			that.formElements.status = 'edit'
			that.showModal 			 = false
		},
		// ---
		saveAction(action) {
			const that = this
			if( ['validate', 'cancel'].includes(action) ) {
				that[`${action}Save`]()
				return
			}
			
			if( that.checkMode == 'removeItem' ) {
				that[that.checkMode]()
				return
			}
			if( that.checkMode == 'editItem' && that.formElements.status == 'edit' ) {
				that.editAction()
				return
			}
			
			that.showModal = true
		},
		editAction() {
			const that = this
			for( let item of that.ToDoElements ) {
				if( that.formElements.id == item.id ) {
					item.title		 = that.formElements.title
					item.description = that.formElements.description
					item.status		 = that.terminated ? 'terminated' : item.status
				}
			}
			that.showModal 		= false
			that.checkMode		= 'create'
			that.terminated		= false
			that.cancelAction()
			// Api
		},
		cancelSave() {
			const that 		= this
			that.checkMode	= 'create'
			that.ToDoElements = that.ToDoElements.filter(item => item.status === 'active')
		},
		validateSave() {
			const that = this   
			
			that.checkMode = 'create'
			that.ToDoElements.forEach( (item) => {
				item.status = item.status == 'draft' ? 'active' : item.status
			} )
			// Api
		},
		checkFeild() {
			const that = this
			if( that.emptyFeild && that.formElements.trim != '' ) {
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
	.doto-items {
		max-height: 300px;
		overflow-y: auto;
		
		.item-active {
			background-color: #eee;
		}
	}
</style>
