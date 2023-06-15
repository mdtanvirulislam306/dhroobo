<template>
    <div>
    
    <section v-if="loading"  class="mt-4">
        <div class="container all_sellers careers">
            <div class="row">
                <div class="col-12 col-lg-12">
                    <div class="title"><h3><p><span style="color: #e95216; font-family: roboto, sans-serif; font-size: 18px; font-weight: bold;">{{ $t('Career') }}</span></p></h3></div>
                </div>
            </div>
            <div v-if="loadCareer" class="row">
                <div v-for="(data, index) in loadCareer" :key="index" class="col-12 col-lg-12">
                    <div class="career-item">
                        <div class="row">
                            <div class="col-md-2">
                                <p class="text-center"><span class="serial_text">{{ (index + 1) }}</span></p>
                            </div>
                            <div class="col-md-8 career-details">
                                <p class="date_item">Circular Date: {{ data.job_date }}</p>
                                <p class="date_item">Application Deadline: {{ data.apply_date }}</p>
                                <p class="post_title text-uppercase"><b>{{ data.position }}</b></p>
                            </div>
                            <div class="col-md-2 text-center">
                                <router-link class="apply_button button" :to="{ name: 'careerSingle', params: {slug: data.id } }"><span>apply now</span></router-link>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
    
    
    <section v-else  class="mt-5">
        <div class="container">
            <div class="row blog_shimmer">
                <div class="col-lg-12 col-md-12 col-lg-12">
                    <div class="row">
                        <div class="col-12 col-sm-12 col-md-12 col-lg-12">
                            <div class="shimmer"> <div class="h_20 w_100per mt_20"></div></div>
                            <div class="shimmer"> <div class="h_3 w_100per mt_20"></div></div>
                            <div class="shimmer"> <div class="h_2 w_100per"></div></div>
                            <div class="shimmer"> <div class="h_2 w_100per"></div></div>
                            <div class="shimmer"> <div class="h_2 w_100per"></div></div>
                            <div class="shimmer"> <div class="h_3 w_5 mt_3 f_right"></div></div>
                        </div>
                    </div>

                </div>
            </div>
        </div>
    </section>
    
    
    
    </div>
    </template>
    
    <script>
    import axios from 'axios'
    import swal from 'sweetalert';
    import Leftsidebar from './default/Leftsidebar';
    import pagination from 'laravel-vue-pagination';
    
    
    
    export default {
        data(){
            return{
                loading:false,
                is_user: '',
                loadCareer:{},
                baseurl:'',
                thumbnailUrl:'',
            }
        },
        components:{
            Leftsidebar,
            pagination
        },
        methods:{
            imageLoadError(event){
                event.target.src = "/images/notfound.png";
            },
           
            getCareerList(){
                let axiosConfig = {
                    headers: {
                    'X-localization': localStorage.getItem('lang')
                    }
                }
                axios.get(this.$baseUrl+'/api/v1/get-career', axiosConfig).then(response => {
                    this.loadCareer = response.data.career;
                    this.loading = true;
                });
            },
    
            scrollToTop() {
                window.scrollTo(0,0);
            },
        },
    
        watch:{
            $route(to, from){
                this.scrollToTop();
            }
        },
    
        mounted(){
            this.scrollToTop();
            this.thumbnailUrl = this.$thumbnailUrl;
            this.getCareerList();
            this.baseurl = this.$baseUrl;
            document.title = "Dhroobo | Career"; 
        }
    
    }
    </script>