<template>
    <div>
    
    <section v-if="loading"  class="mt-4">
        <div class="container all_sellers careers">
            <div class="row">
                <div class="col-12 col-md-6 col-sm-12 col-lg-7">
                    <div v-if="careerSingle" class="title"><h3><p><span style="color: #008000; font-family: roboto, sans-serif; font-size: 18px; font-weight: bold;">{{ $t('Career') }} - {{ careerSingle.position }}</span></p></h3></div>
                </div>

                <div class="col-12 col-md-6 col-sm-12 col-lg-5">
                    <div  class="title"><h3><p><span style="color: #008000; font-family: roboto, sans-serif; font-size: 18px; font-weight: bold;">{{ $t('Apply here') }}</span></p></h3></div>
                </div>
            </div>

            <div class="row">
                <div class="col-12 col-md-6 col-sm-12 col-lg-7">
                    <div class="career-details">
                        <b>Circular Date: {{ careerSingle.job_date }}</b><br>
                        <b>Application Deadline: {{ careerSingle.apply_date }}</b>
                        <p class="pb-5" v-html="careerSingle.description"></p>
                    </div>
                </div>

                <div class="col-12 col-md-6 col-sm-12 col-lg-5">
                    <div class="card border_site rounded-0">
                        <div class="card-body p-3">
                            <div class="form-group">
                                <label> {{ $t('First Name') }} <span style="color:#f00">*</span> </label>
                                <div class="input-group">
                                    <input value="" type="text" name="first_name" class="form-control" id="first_name" :placeholder="$t('First Name')+'..'">
                                    <div class="validation_error" v-if="errors.first_name" v-html="errors.first_name[0]" />
                                </div>
                            </div>
                            <div class="form-group">
                                <label> {{ $t('Last Name') }} <span style="color:#f00">*</span> </label>
                                <div class="input-group">
                                    <input value="" type="text" name="last_name" class="form-control" id="last_name" :placeholder="$t('Last Name')+'..'">
                                    <div class="validation_error" v-if="errors.last_name" v-html="errors.last_name[0]" />
                                </div>
                            </div>
                            <div class="form-group">
                                <label>{{ $t('Phone') }} <span style="color:#f00">*</span></label>
                                <div class="input-group mb-2 mb-sm-0">
                                    <input type="text" name="phone" class="form-control" id="phone" :placeholder="$t('Phone')+'..'">
                                    <div class="validation_error" v-if="errors.phone" v-html="errors.phone[0]" />
                                </div>
                            </div>
                            <div class="form-group">
                                <label>{{ $t('Email') }}</label>
                                <div class="input-group mb-2 mb-sm-0">
                                    <input type="email" name="email" class="form-control" id="email" :placeholder="$t('Email')+'..'">
                                    <div class="validation_error" v-if="errors.email" v-html="errors.email[0]" />
                                </div>
                            </div>
                            <div class="form-group">
                                <label> {{ $t('Resume') }} <span style="color:#f00">*</span></label>
                                <div class="input-group mb-2 mb-sm-0">
                                    <input type="file" name="resume" class="form-control" id="resume" >
                                    <div class="validation_error" v-if="errors.resume" v-html="errors.resume[0]" />
                                </div>
                            </div>
                            <div class="form-group">
                                <label>{{ $t('Cover Letter') }} <span style="color:#f00">*</span></label>
                                <div class="input-group mb-2 mb-sm-0">
                                    <textarea type="text" class="form-control" name="cover_letter" id="cover_letter" :placeholder="$t('Write cover letter')" rows="3" cols=""></textarea>
                                    <br>
                                    <div class="validation_error" v-if="errors.cover_letter" v-html="errors.cover_letter[0]" />
                                </div>
                            </div>
                            <div class="text-center">
                                <input type="hidden" name="job_id" id="job_id" :value="careerSingle.id" />
                                <input type="submit" name="submit" :value="$t('SEND')" @click.prevent="applyforjobform()" class="btn btn-primary btn-block rounded-0 py-2">
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
                <div class="col-12 col-md-6 col-lg-7">
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
                <div class="col-12 col-md-6 col-lg-5">
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
                careerSingle:'',
                baseurl:'',
                thumbnailUrl:'',
                errors:'',
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
           
            load_single_career(){
                let axiosConfig = {
                    headers: {
                    'X-localization': localStorage.getItem('lang')
                    }
                }
                let slug = this.$route.params.slug;
                axios.get(this.$baseUrl+'/api/v1/get-career-details/'+slug, axiosConfig).then(response => {
                    this.careerSingle = response.data.career;
                    this.loading = true;
                });
            },

            applyforjobform(){
                let formData = new FormData();
                //Personal info
                formData.append('first_name', $('#first_name').val());
                formData.append('last_name', $('#last_name').val());
                formData.append('phone', $('#phone').val());
                formData.append('email', $('#email').val());
                formData.append('resume', $('#resume')[0].files[0]);
                formData.append('cover_letter', $('#cover_letter').val());
                formData.append('job_id', $('#job_id').val());
                axios.post(this.$baseUrl+'/api/v1/apply/for/job', formData).then(response => {
                    if(response.data.status == 1){
                        swal({
                            title: 'Message sent successfully.',
                            icon: "success",
                            timer: 3000
                        }).then(()=>{
                            $('#first_name').val('');
                            $('#last_name').val('');
                            $('#phone').val('');
                            $('#email').val('');
                            $('#resume').val('');
                            $('#cover_letter').val('');
                            $('.validation_error').hide();
                        });
                    }else{
                        swal({
                            icon: 'error',
                            title:  response.data.message,
                            showConfirmButton: false,
                            timer: 2000,
                        });
                        // this.errors = response.data.message;
                    }
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
            this.load_single_career();
            this.baseurl = this.$baseUrl;
            document.title = "Dhroobo | Career Details"; 
        }
    
    }
    </script>