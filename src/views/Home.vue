<template>
	<div class="fill-height" v-touch="{
		right: _ => drawer = true
	}">

		<v-text-field color="pink accent-3" v-model="search" dark placeholder="Search..." class="mx-3 my-3" outlined />

		<!-- Notes Cards -->

		<div v-if="load">
			<div v-for="i in lts" :key="i.name">
				<v-card color="teal" class="mx-3 my-2" dark :href="'#/read/'+i.id">
					<v-card-title>
						{{ i.name }}
					</v-card-title>
				</v-card>
			</div>
		</div>

		<div v-else>
			<Load/>
		</div>

		<v-btn color="pink accent-3" href="#/add" fab bottom fixed right dark>
			<v-icon>{{ plus }}</v-icon>
		</v-btn>

		<!-- Menu -->

		<v-navigation-drawer
	      v-model="drawer"
	      absolute
	      dark
	    >

	    	<v-list-item>

	    		<v-list-item-avatar>
	    			<v-img :src="user.photoURL"></v-img>
	    		</v-list-item-avatar>

	    		<v-list-item-content>
	    			<v-list-item-title>
	    				{{ user.displayName }}
	    			</v-list-item-title>
	    		</v-list-item-content>

	    	</v-list-item>

	    	<v-divider></v-divider>

	    	<v-list>

	    		<h2>Trash:</h2>

	    		<div v-for="i in trash">
	    			<v-list-item :href="`#/trash/${i.id}`">

		    			<v-list-item-content>
		    				<v-list-item-title>
		    					{{ i.name }}
		    				</v-list-item-title>
		    			</v-list-item-content>


		    		</v-list-item>

		    		<v-divider></v-divider>
	    		</div>

	    	</v-list>
      
    	</v-navigation-drawer>
		
	</div>
</template>


<script lang="ts">
	import Load from '../components/Load.vue'
	import { mdiPlus } from '@mdi/js';
	import { db, auth } from '../firebase';

	export default {
		created() {

			auth.onAuthStateChanged(async user => {
				if (user) {
					
					this.user = user;

					db.collection('notes').where('uid', '==', user.uid).onSnapshot(docs => {
						this.lts = []

						this.load = true;

						docs.forEach(doc => {
							this.lts.push({name: doc.data().name, id: doc.id});
							this.lts_back.push({name: doc.data().name, id: doc.id});
						})

						console.log(user.uid)
					});

					db.collection('trash').where('uid', '==', user.uid).onSnapshot(docs => {
						docs.forEach(doc => {
							this.trash.push({name: doc.data().name, id: doc.id});
						})

					});

				} else {
					location.href = '#/login';
				}
			})

		},
		data() {
			return {
				plus: mdiPlus,
				lts: [],
				lts_back: [],
				search: '',
				drawer: false,
				user: {},
				trash: [],
				load: false
			}
		},
		methods: {
			yolo() {
				console.log('yolo')
			}
		},
		watch: {
			search(val) {
				console.log(val);

				this.lts = []

				this.lts_back.forEach(doc => {
					const filter = doc.name.includes(val);
					console.log(filter)

					if (filter) {
						this.lts.push(doc)
					}
				})
			}
		},
		components: {
			Load
		}
	}
</script>