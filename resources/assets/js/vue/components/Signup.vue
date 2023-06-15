<template>
<div>




<section id="register-page">
    <div class="container">
        <div class="row">
            <div class="col-12 col-sm-12 col-md-1 col-lg-2 col-xl-2"></div>
            <div class="col-12 col-sm-12 col-md-10 col-lg-8 col-xl-8">
                <div class="register-form mt-5 mb-5">
                    <h4>{{ $t('Create an Account') }}</h4>
                    <form @submit.prevent="signup">
                        <!-- <div class="form-group">
                            <div class="singin-with-google">Signin with google</div>
                        </div> -->

                     <!-- <input type="hidden" name="affiliate_referer" :value="affiliate_referer"> -->
                      
                      <div class="row">
                          <div class="col-md-6">
                                <div class="form-group">
                                    <label for="">{{ $t('Full Name') }}<span style="color:#f00">*</span></label>
                                    <input v-model="signupForm.name" type="text" name="name" class="form-control" :placeholder="$t('Full Name')+'..'" required>
                                    <div class="validation_error" v-if="errors.name" v-html="errors.name[0]" />
                                </div>
                          </div>
                           <div class="col-md-6">
                                <div class="form-group">
                                    <label for=""> {{ $t('Mobile number') }}<span style="color:#f00">*</span></label>
                                    <input v-model="signupForm.phone" type="text" name="phone" class="form-control" :placeholder="$t('Enter Mobile Number')" required>
                                    <div class="validation_error" v-if="errors.phone" v-html="errors.phone[0]" />
                                </div>
                          </div>
                      </div>

                        <div class="row">

                          <div class="col-md-12">
                                <div class="form-group">
                                    <label for="">{{ $t('Email') }}</label>
                                    <input v-model="signupForm.email" type="email" name="email" class="form-control" :placeholder="$t('Email')+'..'">
                                    <div class="validation_error" v-if="errors.email" v-html="errors.email[0]" />
                                </div>
                          </div>
                      </div>

                        <div class="row">
                          <div class="col-md-6">
                                <div class="form-group">
                                    <label for="">{{ $t('Password') }}<span style="color:#f00">*</span></label>
                                    <input v-model="signupForm.password" type="password" name="password"  class="form-control" :placeholder="$t('Password')+'..'" required>
                                    <div class="validation_error" v-if="errors.password" v-html="errors.password[0]" />
                                </div>
                          </div>

                          <div class="col-md-6">
                                <div class="form-group">
                                    <label for="">{{ $t('Confirm Password') }}<span style="color:#f00">*</span></label>
                                    <input v-model="signupForm.password_confirmation" type="password"  name="password_confirmation" class="form-control" :placeholder="$t('Confirm Password')+'..'" required>
                                    <div class="validation_error" v-if="errors.password_confirmation" v-html="errors.password_confirmation" />
                                </div>
                          </div>
                        </div>
                      
                        <div class="row">
                            <div class="col-12 col-sm-12 col-md-2"></div>
                            <div class="col-12 col-sm-12 col-md-8">
                                <div class="form-group">
                                    <input type="submit" class="singin-with-google" :value="$t('Sign up now')">
                                </div>
                            </div>
                            <div class="col-12 col-sm-12 col-md-2"></div>
                        </div>





                        <div class="row">
                            <div class="col-12 col-sm-12 col-md-6 already-account">
                                <p> {{ $t('Already have a accont') }} ? </p>
                            </div>
                            <div class="col-12 col-sm-12 col-md-6 ">
								 <b class="text-uppercase" ><router-link :to="{name: 'login'}">{{ $t('Sign in now') }}</router-link></b> 
                            </div>
                        </div>
                    </form>
                </div>
            </div>
            <div class="col-12 col-sm-12 col-md-1 col-lg-2 col-xl-2"></div>
        </div>
    </div>
</section>






</div>
</template>




<script>
import Form from 'vform'
import axios from 'axios'


export default {
  data: () => ({
    signupForm: new Form({
      name: '',
	  phone: '',
	  email: '',
	  password: '',
      password_confirmation: '',
      affiliate_referer: '',
    }),
    errors:{},
	show:false,
    errors: [],
  }),

  methods: {

    async signup(){
      const response = await this.signupForm.post(this.$baseUrl + "/api/v1/user-register");
      if(response.data.status == 2){
        swal({
            title: response.data.message,
            icon: "error",
            timer: 4000
        });
      }else if(response.data.status == 1){
        swal({
            title: "Your account has been successfully created. You are logged in.",
            icon: "success",
            timer: 4000
        }).then(()=>{
            localStorage.setItem("token", response.data.token);
            this.$store.dispatch('loadedUser');
            this.$store.dispatch('loadedCart');
            this.$store.dispatch('loadedCompares');
            this.$store.dispatch('loadedNotifications');
            this.$router.push({name:'myaccount'});
        });
	  }else{
          this.errors = response.data.message;
      }
    },
  },
  mounted(){
    this.affiliate_referer = localStorage.getItem("affiliate_referer");
  }
  
}
</script>












