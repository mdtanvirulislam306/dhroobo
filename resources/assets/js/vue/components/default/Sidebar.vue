<template>
    <div >
            <nav id="sidebar" class="not_active">
                <div id="sidebar_child">
                    <div class="menu_wrapper">
                        <ul class="list-unstyled components">
                            <!-- main menu start-->
                            <li class="active main_menu_sidebar">
                                <a href="#homeSubmenu" data-toggle="collapse" aria-expanded="false" class="dropdown-toggle collapsed"><i class="fa fa-bars" aria-hidden="true"></i>  MAIN MENU</a>
                            
                                <ul class="list-unstyled collapse" id="homeSubmenu" style="">
                                    <li v-for="(navbar, index) in navbars" :key="index" v-if="navbar.status == '1'">
                                        <span v-if="navbar.link_type == 'Internal'" >
                                            <router-link :to="{name: navbar.link}">  {{ navbar.title }} </router-link>
                                        </span>
                                        <span v-else>
                                            <a target="_blank" :href="navbar.link">{{ navbar.title }}</a>
                                        </span>
                                    </li>
                                </ul>
                            </li>
                            <li> <div class="category_start text-uppercase">{{ $t('Categories') }}</div> </li>
                            <!-- main menu end-->
                        </ul>
                        <ul class="list-unstyled components mdn-accordion blue-accordion-theme" id="layer_0">
                        <li v-for="(category1, index) in categories" :key="index" class="sub-level" v-if="category1.categories.length > 0" >
                            
                            <input class="accordion-toggle" type="checkbox" :name ="'group-'+index" :id="'group-'+index">
                            <router-link  class="close_mobile_nav accordion-title link_title" :to="{name: 'category', params: {slug: category1.slug } }"> <span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category1.image">  </span> {{ category1.title }}</router-link>
                            <label class="accordion-title link_level" :for="'group-'+index"> </label>
                            <ul id="layer_1">
                            <li v-for="category2 in category1.categories" :key="category2.id" v-if="category2.categories.length > 0" class="sub-level">
                                <input class="accordion-toggle" type="checkbox" :name ="'sub-group-'+category2.id" :id="'sub-group-'+category2.id">
                                <!-- <label class="accordion-title" :for="'sub-group-'+category2.id">{{ category2.title }}</label> -->

                                <router-link  class="close_mobile_nav accordion-title link_title" :to="{name: 'category', params: {slug: category2.slug } }"><span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category2.image">  </span>{{ category2.title }}</router-link>
                                <label class="accordion-title link_level" :for="'sub-group-'+category2.id"> </label>
                                <ul id="layer_2">
                                    <li v-for="category3 in category2.categories" :key="category3.id" v-if="category3.categories.length > 0" class="sub-level">
                                        <input class="accordion-toggle" type="checkbox" :name ="'sub-group-level-'+category3.id" :id="'sub-group-level-'+category3.id">
                                        <!-- <label class="accordion-title" :for="'sub-group-level-'+category3.id">{{ category3.title }}</label> -->
                                        <router-link  class="close_mobile_nav accordion-title link_title" :to="{name: 'category', params: {slug: category3.slug } }"><span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category3.image">  </span>{{ category3.title }}</router-link>
                                        <label class="accordion-title link_level" :for="'sub-group-level-'+category3.id"> </label>
                                        <ul id="layer_3">
                                        <li v-for="category4 in category3.categories" :key="category4.id" >
                                            <router-link :to="{name: 'category', params: {slug: category4.slug } }"><span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category4.image">  </span>{{ category4.title }}</router-link>
                                        </li>
                                        </ul>
                                    </li>
                                    <li v-else>
                                        <router-link class="close_mobile_nav width100" :to="{name: 'category', params: {slug: category3.slug } }" ><span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category3.image">  </span>{{ category3.title }} </router-link>
                                    </li>
                                </ul>
                            </li>
                            <li v-else>
                                <router-link class="close_mobile_nav width100" :to="{name: 'category', params: {slug: category2.slug } }" ><span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category2.image">  </span>{{ category2.title }} </router-link>
                            </li>
                            </ul>
                        </li>
                        <li v-else>
                            <router-link class="close_mobile_nav width100" :to="{name: 'category', params: {slug: category1.slug } }"><span class="category_image"> <img @error="imageLoadError" :src="baseurl+'/'+category1.image">  </span>{{ category1.title }}</router-link>
                        </li>
                        </ul>
                    </div>
                </div>
            </nav>
    </div>
</template>





<script>
import axios from 'axios';

export default {
  data(){
		return{
            userData:'',
            navbars:'',
            categories:[],
             baseurl:'',
		}
	},
  methods: {
       imageLoadError(event){
             event.target.src = "/images/notfound.png";
       },
        site_information(){
            let axiosConfig = {
            headers: {
                'X-localization': localStorage.getItem('lang')
            }
            }
            axios.get(this.$baseUrl+'/api/v1/get-navbars', axiosConfig).then(response => {
                this.navbars = response.data
            });
        },

        load_categories(){
            let axiosConfig = {
            headers: {
                'X-localization': localStorage.getItem('lang')
            }
            }
            axios.get(this.$baseUrl+'/api/v1/categories', axiosConfig).then(response => {
            this.categories = response.data;
            });
        },



  },


    watch:{
       $route(){
        // $('#sidebar').addClass('not_active');
        // $('.bar_child').css({'left':'18vw'});
      }
    },
  mounted(){
    this.baseurl = this.$baseUrl;
    this.load_categories();
    this.site_information();
    //this.getUserDetails();



  }
}
</script>