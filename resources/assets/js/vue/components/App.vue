<!-- eslint-disable vue/html-indent -->
<!-- eslint-disable vue/html-self-closing -->
<template>
    <div :class="'page_'+$route.name" id="app">
		<compHeader></compHeader>
		<div class="wrapper d-flex align-items-stretch">
			<Sidebar></Sidebar>
			<div id="full_flex" class="full_flex">
				<router-view class="middle_component"> </router-view >
				<compFooter></compFooter>
			</div>
		</div>
	</div>
</template>



<script>
import compHeader from './default/Header';
import compFooter from './default/Footer';
import Sidebar from './default/Sidebar';



export default {
	data(){
		return{
			fname:'',
			loadedData:[],
		}
	},
	components:{
		compHeader,
		Sidebar,
		compFooter
	},
	methods:{
		scrollToTop(){
			window.scrollTo(0,0);
		}
	},
	mounted(){
		this.scrollToTop();
		this.$store.dispatch('loadedUser');
		this.$store.dispatch('loadedCart');
		this.$store.dispatch('loadedCompares');
		
		
	},
}

</script>





