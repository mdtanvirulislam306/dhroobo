<!-- eslint-disable vue/html-indent -->
<template>
<div>


<section v-if="authenticated">
    <div class="container">
	<div class="col-md-12 account_wrapper bg_white">
		<div class="row profile">
			<div class="col-md-1">
				<img @error="imageLoadError" :src="baseurl+'/'+userData.avatar" >
			</div>
			<div class="col-md-11">
				<div class="username">
					<b>{{ userData.name  }}</b>
					<p class="mb-0">{{ userData.email }}</p>
                    <p v-if="userData.user_type == 2"> <span class="badge badge-danger">{{$t('Corporate Customer')}}</span> </p>
				</div>
			</div>
		</div>

        <div class="row account_box">
            <div class="col-md-3 col-lg-3 profile-navigation">
                <div class="profile-nav">
                    <Leftsidebar></Leftsidebar>
                </div>
            </div>
			
            <div class="col-md-9 col-lg-9">

            <div class="form-title text-left">
            <h5><b>{{ $t('Notifications') }}</b></h5>
            </div>
            <div class="fulltab">
            
                <div id="list" class="mytab list_tab">
                    <table  width="100%" class="table table-hover text-left">
                        <thead>
                            <tr>
                                <th  width="10%"> {{ $t('No') }} </th>
                                <th  width="80%">{{ $t('Notifications') }}</th>
                                <th  width="10%">{{ $t('Action') }}</th>
                            </tr>
                        </thead>
                        <tbody  v-if="notificationsData.notification_total > 0">
                            <tr v-for="(notify, index) in notificationsData.notification" :key="index"> 
                                <td class="text-crnter"> <div class="notifyno">{{index+1}}</div></td>
                                <td> {{ notify.decoded_description.message }}  </td>
                                <td @click.prevent="viewNotification(notify.id,notify.decoded_description)" class="viewnoty">{{$t('Mark as Seen')}} </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            
            </div>
        </div>
			
        </div>
    </div>
	</div>
</section>

<section v-else id="profile-page">
    <div class="container">
	<div class="col-md-12 account_wrapper bg_white">
		<div class="row profile">
			<div class="col-md-1">
				<div class="shimmer">
					<div class="circle_100"></div>
				</div>
			</div>
			<div class="col-md-11">
				<div class="shimmer">
					<div class="h_2 w_10 mt_25"></div>
					<br/>
					<div class="h_2 w_10"></div>
				</div>
			</div>
		</div>
        <div class="row account_box">
            <div class="col-md-3 col-lg-3 profile-navigation">
                <div class="profile-nav">
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
					<div class="shimmer ">
						<div class="h_3 w_2 mb_10"></div>
						<div class="h_3 w_13 mb_10"></div>
					</div>
                </div>
            </div>
            <div class="col-md-9 col-lg-9">
                <div class="form-section" id="myaccountform">
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                    <div class="shimmer "><div class="h_3 w_100per mb_10"></div></div>
                </div>
            </div>
        </div>
		</div>
    </div>
</section>




</div>
</template>






<script>
import Form from 'vform'
import axios from 'axios'
import swal from 'sweetalert';
import Leftsidebar from './default/Leftsidebar';
export default {
	data(){
		return{
			is_user: '',
			userData:'',
			image:'',
			profile_picture:'',
			baseurl:'',
            addresses: {},
            districts:{},
            upazilas:{},
            unions:{},
            errors:{},
            errors: [],
            authenticated: false
		}
	},
	components:{
		Leftsidebar
	},
	methods:{
        imageLoadError(event){
            event.target.src = "/images/notfound.png";
        },
       viewNotification(notification_id,notification){
           let token = localStorage.getItem("token");
           let axiosConfig = {
             headers: {
               'Content-Type': 'application/json;charset=UTF-8',
               "Access-Control-Allow-Origin": "*",
               'Authorization': 'Bearer '+token
             }
           }
           axios.post(this.$baseUrl+'/api/v1/view-notification', {notification_id:notification_id}, axiosConfig).then(response => {
             if(response.data.status == 1){
               this.$store.dispatch('loadedNotifications');

               if(notification.type == 'order'){
                  this.$router.push({name: 'orderDetails', params: {id: notification.order_id } });
               }else if(notification.type == 'deal'){
                  this.$router.push({ name: 'flashdeal', params: {slug: notification.deal_slug } });
               }else if(notification.type == 'cart'){
                  jQuery('.left_cart_icon').trigger('click');
               }

               
             }else{
               swal("Sorry" , response.data.message,  "error" );
             }
           }); 
       },
        checkAuth(){
            let token = localStorage.getItem("token");
            let axiosConfig = {
                headers: {
                    'Content-Type': 'application/json;charset=UTF-8',
                    "Access-Control-Allow-Origin": "*",
                    'Authorization': 'Bearer '+token
                }
            }
            axios.post(this.$baseUrl+'/api/v1/checkauth',{},axiosConfig).then(response =>{
                if(response.data.status != 1){
                    this.$router.push({name:'login'});
                    swal ( "Oops", response.data.message, "error");
                }else{
                    this.authenticated = true;
                }
            });
        },
    	scrollToTop(){
            window.scrollTo(0,0);
        }

	},
	computed:{
        logged_in_user(){
            return this.$store.getters.getLoadedUser.user;
        },
        notificationsData(){
            return this.$store.getters.getLoadedNotifications;
        },
	},
	mounted(){

       const plugin = document.createElement("script");
       plugin.setAttribute( "src",this.$frontendUrl+"/assets/js/custom.js");
       plugin.async = true;
       document.body.appendChild(plugin);


        this.scrollToTop();
        this.checkAuth();
		this.baseurl = this.$baseUrl;
		document.title = "Dhroobo | Update Address";  
	}
}
</script>