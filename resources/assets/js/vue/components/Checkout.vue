<template>
<div>
<div v-if="loading" p-0>
    <section v-if="cartData.sub_total > 0" id="cart-page">
    <div class="container">
        <div class="row cart-page-container">
            <div class="col-12 col-sm-12 col-md-8 col-lg-8 pr-0">
                <div class="row shoping-cart-text">
                    <div class="col-12 col-sm-12 col-md-12">
                        <h4>{{ $t('Checkout') }}</h4>
                    </div>
                </div>
                <div class="cart-calculation">
                    <table  class="table text-left cart_table" width="100%">
                        <thead>
							<tr>
								<th width="65%">{{ $t('Product Details') }}</th>
								<th width="25%"> {{ $t('Price') }}</th>
								<th class="text-center" width="10%"> {{ $t('Quantity') }}</th>
							</tr>
                        </thead>
                       
					   
					    <tbody v-for="(cartgroup, index) in cartData.cart" :key="index">
							
							<tr class="group_header mb-3">
								<td colspan="7">
									<small> {{ $t('Shipped by') }} : <b> <router-link class="text-dark" :to="{ name: 'shop', params: {slug: cartgroup.shop_info.shop_slug } }" >{{cartgroup.shop_info.shop_name}}</router-link></b></small>
								</td>
							</tr> 

							<tr v-for="(cart, index) in cartgroup.items" :key="index" class="cart_product_group">
							
								<td> 
									<div class="table-item">
                                            <div class="media">
                                                <img @error="imageLoadError"  class="mr-3 product-cart-img" :src="baseurl+'/'+cart.product.default_image" alt="">
                                                <div class="media-body">
                                                    <h5 class="mt-0"> <router-link :to="{ name: 'product', params: {slug: cart.product.slug } }"> {{ cart.product.title }} </router-link></h5>
                                                    <div v-if="cart.product_type != 'digital' && logged_in_user_address != 0 && logged_in_user.default_address_id != null" class="select_shipping_options">
                                                            <ul v-if="cart.shipping_options != 0" class="list-group list-group-horizontal">
                                                                <li :data-product-id="cart.product_id"  data-shipping-method="free_shipping" :data-shipping-cost="0" :data-qty="cart.qty" v-if="cart.shipping_options.free_shipping == 'on'" class="list-group-item"> BDT 0 <br> {{ $t('Free Shipping') }}  <br>  {{ $t('Est. Arrival: Within 7 to 15 days') }} </li>
                                                                <li :data-product-id="cart.product_id" data-shipping-method="standard_shipping" :data-shipping-cost="cart.shipping_options.standard_shipping" :data-qty="cart.qty" v-if="cart.shipping_options.standard_shipping > 0" class="list-group-item selected_shipping" >BDT {{ cart.shipping_options.standard_shipping }}  <br>{{ $t('Standard Shipping') }}  <br> {{ $t('Est. Arrival: Within 4 to 7 days') }} </li>
                                                                <li :data-product-id="cart.product_id" data-shipping-method="express_shipping" :data-shipping-cost="cart.shipping_options.express_shipping" :data-qty="cart.qty" v-if="cart.shipping_options.express_shipping > 0" class="list-group-item">BDT {{ cart.shipping_options.express_shipping }} <br> {{ $t('Express Shipping') }}  <br> {{ $t('Est. Arrival: Within 1 to 3 days') }} </li>
                                                            </ul>
                                                             <ul v-else class="list-group list-group-horizontal">
                                                                <li :data-product-id="cart.product_id" data-shipping-method="standard_shipping" :data-shipping-cost="150" :data-qty="cart.qty" class="list-group-item selected_shipping">BDT 150 <br> {{ $t('Standard Shipping') }}  <br> {{ $t('Est. Arrival: Within 4 to 7 days') }}</li>
                                                            </ul>
                                                    </div>

                                                    <span  v-if="cart.product_type == 'variable'">
                                                        <p class="mb-0 text-capitalize font-13" v-for="(vOption,key) in cart.variable_options" :key="key"> <b>{{key}}</b> : {{vOption}}</p>
                                                    </span>
                                                    <span  v-if="cart.product_type == 'digital'">
                                                        <p v-if="cart.variable_options" class="mb-0 text-capitalize font-13"> <b>{{ $t('Contact Number') }}</b> : {{cart.variable_options}}</p>
                                                    </span>
                                                </div>
                                            </div>

									</div> 
								</td>
								<td> <div class="table-item">BDT {{ cart.price }}</div> </td>
								<td class="text-center" > <div class="table-item">{{ cart.qty }}</div> </td>
							</tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <div class="col-12 col-sm-12 col-md-4 col-lg-4 payment">
                <h5>{{ $t('Shipping information') }}</h5>

                <div v-if="logged_in_user_address != 0 && logged_in_user.default_address_id != null" class="address_details">
                    <ul  v-for="(address,index) in logged_in_user_address" :key="index" v-if="logged_in_user.default_address_id == address.id" >
                        <li> 

                            <div class="row p-0">
                                <div class="col-lg-1 pr-0"><b><i class="fa fa-map-marker" aria-hidden="true"></i></b></div>
                                <div class="col-lg-10 p-0"> <span>{{  address.division.title +', '+address.district.title+', '+address.upazila.title+', '+ address.union.title+', '+address.shipping_address }}</span> </div>
                                <div class="col-lg-1 pl-0"><i class="fa fa-pencil address_btn" data-toggle="modal" data-target="#addressModal" aria-hidden="true"></i></div>
                            </div>
                        </li>
                        <li> <b><i class="fa fa-phone" aria-hidden="true"></i></b> <span>{{address.shipping_phone}}</span></li>
                        <li v-if="address.shipping_email"> <b><i class="fa fa-envelope-o" aria-hidden="true"></i></b> <span>{{address.shipping_email}}</span> </li>
                    </ul>    
                </div>
                <div v-else  class="address_details_alt"> 
                    
                    <div class="row p-0">
                        <div class="col-lg-1 pr-0"><b><i class="fa fa-map-marker" aria-hidden="true"></i></b></div>
                        
                        <div v-if="logged_in_user.default_address_id == null" class="col-lg-10 p-0"> <p class="required_addtess" data-required-address="true">{{ $t('You need to select your default shipping address') }}.</p> </div>
                        <div v-else class="col-lg-10 p-0"> <p class="required_addtess" data-required-address="true">{{ $t('You need to add your shipping address') }}.</p> </div>

                        <div class="col-lg-1 pl-0"><i class="fa fa-pencil address_btn" data-toggle="modal" data-target="#addressModal" aria-hidden="true"></i></div>
                    </div>
                            
                </div>

                <div class="note">
                     <textarea type="text" v-model="note" class="form-control form_note" rows="3" :placeholder="$t('Write a note here')+'..'"></textarea>
                    <div id="addCouponBlock">
                        <p class="mt-3">  {{ $t('Add Coupon') }} </p>
                        <div class="input-group mb-3">
                            <input id="couponeCode" type="text" class="form-control" :placeholder="$t('Write a coupon code here')+'..'"  aria-describedby="basic-addon2">
                            <div class="input-group-append">
                                <span class="input-group-text text-center d-block" @click.prevent="applyCouponCode()" id="basic-addon2"> {{ $t('Apply') }}</span>
                            </div>
                        </div>
                    </div>
                </div>


                <div v-if="collectedVoucher.length" class="collect_voucher_modal">
                    <h5>{{ $t('Collected Voucher') }}</h5>
                    <div  v-for="(cv,index) in collectedVoucher" :key="index">
                        <p><input type="hidden" name="collected_voucher" class="collected_voucher_radio" :value="cv.voucher_id" required> 
                        <img @error="imageLoadError" style="width:100%" :src="baseurl+'/'+cv.voucher.banner" alt="">
                        </p>
                    </div>
                </div>

                <div class="voucher_button mt-3">
                    <ul class="list-group list-group-horizontal">
                        <li v-if="useableVouchers.length" style="width:100%" class="list-group-item">
                            <a data-toggle="modal" data-target=".use_voucher_modal" href="javascript:void(0)">
                                <p class="text-center mb-0"> {{ $t('Use Voucher') }}</p>
                            </a> 
                            
                        </li>
                    </ul>
                </div>


                <div class="paymentmethod mt-3">
                     <h5> {{ $t('Payment Method') }}</h5>
                    <ul class="list-group list-group-horizontal">
                        <li data-payment-method="online_payment" class="list-group-item selected_payment" > 
                            <p class="text-center mb-0"> <img @error="imageLoadError" src="/images/ssl.png" alt=""> <br><b>{{ $t('Online Payment') }}</b></p>
                        </li>
                        <li data-payment-method="cash_on_delivery" class="list-group-item"> 
                            <p class="text-center mb-0"> <img @error="imageLoadError" src="/images/cod1.png" alt=""> <br><b> {{ $t('Cash On Delivery') }}</b></p>
                        </li>
                    </ul>
                </div>
                <div class="payment-calculation mt-3 mb-4">
                      <h5>{{ $t('Order Summary') }}</h5>
					<ul>
						<li :data-subtotal-amount="sub_total" class="data_sub_total"> <b> {{ $t('Sub Total') }} </b>  <span v-if="sub_total"> BDT&nbsp;{{ sub_total }}</span> <span v-else> BDT {{ cartData.sub_total }} </span></li>
						<li :data-shipping-cost="cartData.shipping_cost" class="shipping_cost_li"> <b>{{ $t('Shipping Cost') }} (+)</b>  <span>BDT&nbsp;<span class="calculatedShipping">{{ cartData.shipping_cost }}</span></span></li>
                        
                        <li v-if="coupon_discount.status == 1" class="coupon_discount" :data-coupon-discount="Number(coupon_discount.amount)" > <b> {{ $t('Coupon Discount') }} (-)<br>  ({{coupon_discount.code}}) <a  @click.prevent="removeCoupon()" class="text-danger" href="javascript:void(0)"> {{ $t('Remove') }} </a></b>  <span>BDT {{ Number(coupon_discount.amount) }}</span></li>
                        <li data-coupon-discount="0" v-else class="coupon_discount"  > <b>{{ $t('Coupon Discount') }} (-) </b>  <span>BDT 0</span></li>
                        <li data-voucher-discount="0" class="show_voucher_discount"  ><b> {{ $t('Voucher Discount') }} (-)</b><span class="v_amount">BDT 0</span></li>
                        <li> <b class="totaprice" id="totalPrice" :data-total-price="sub_total+cartData.shipping_cost" > {{ $t('Total') }} </b>  <span> BDT&nbsp;<span class="calculatedTotal"> {{ finalCalculatedTotal}}</span></span></li>
					</ul>
                </div>
                <div class="procced-checkout mt-3">
                    <ul>
                        <li> <input type="checkbox" value="agree" class="agree"> <small> {{ $t('I agree to the terms and Conditions') }}</small></li>
                        <li> <button class="btn btn-primary site_color1 proceed_to_pay" @click.prevent="proceedToPay()"> {{ $t('Proceed To Pay') }} </button> </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
    </section>



	<section v-else  id="cart-page-shimmer">
	<div class="container">
		<div class="row">
			<div v-if="cartData.sub_total == 0 || cartData.status == 0" class="col-md-12">
				<p> <img @error="imageLoadError" src="/images/no-item-in-cart.gif" alt="">  </p>
				<h4> {{ $t('No product in cart') }} ! </h4>
				<p> <router-link :to="{name:'products'}"> {{ $t('Continue shopping') }} </router-link></p>
			</div>
			<div v-else class="col-md-12 simar_lodding">
			
			<div class="container">
				<div class="row cart-page-container">
					<div class="col-12 col-sm-12 col-md-8 col-lg-9 pr-0">
						<div class="row shoping-cart-text">
							<div class="col-12 col-sm-12 col-md-12">

								<div class="shimmer">
									<div class="h_3 w_10 ml_10 mt_5"></div>
								</div>

							</div>
						</div>
						<div class="cart-calculation">
							<table  class="table text-left cart_table" width="100%">
								<thead>
									<tr>
										<th width="5%"> <div class="shimmer"><div class="h_2 w_5"></div></div> </th>
										<th width="60%"></th>
										<th width="5%"> </th>
										<th width="5%"> </th>
										<th width="20%">  <div class="shimmer"><div class="h_2 w_5"></div></div> </th>
										<th width="5%" style="text-align: right;">  <div class="shimmer"><div class="h_2 w_5"></div></div></th>
									</tr>
								</thead>
								<tbody>
									<tr class="group_header mb-3">
										<td colspan="7">
											<small><div class="shimmer"><div class="h_2 w_7"></div></div>  </small>
										</td>
									</tr> 
									<tr class="cart_product_group">
										<td> 
                                            <div class="product-cart-img">
                                                <div class="shimmer"><div class="h_10 w_6 mr_5"></div></div>  
                                            </div> 
                                        </td>
										<td> 
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                        </td>
										<td> </td>
										<td> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div></td>
									</tr>

									<tr class="group_header mb-3">
										<td colspan="7">
											<small><div class="shimmer"><div class="h_2 w_7"></div></div>  </small>
										</td>
									</tr> 
									<tr class="cart_product_group">
										<td> 
                                            <div class="product-cart-img">
                                                <div class="shimmer"><div class="h_10 w_6 mr_5"></div></div>  
                                            </div> 
                                        </td>
										<td> 
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                        </td>
										<td> </td>
										<td> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div></td>
									</tr>
									<tr class="group_header mb-3">
										<td colspan="7">
											<small><div class="shimmer"><div class="h_2 w_7"></div></div>  </small>
										</td>
									</tr> 
									<tr class="cart_product_group">
										<td> 
                                            <div class="product-cart-img">
                                                <div class="shimmer"><div class="h_10 w_6 mr_5"></div></div>  
                                            </div> 
                                        </td>
										<td> 
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                        </td>
										<td> </td>
										<td> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div></td>
									</tr>
								</tbody>
							</table>
						</div>
					</div>
					<div class="col-12 col-sm-12 col-md-4 col-lg-3 payment">
						<div class="note">
							<div class="shimmer"><div class="h_2 w_48per mb_5"></div></div>
						</div>
						<div class="payment-calculation">
							<ul>
								<li> <div class="shimmer"><div class="h_10 w_100per mb_5"></div></div> </li>
								<li> <div class="shimmer"><div class="h_7 w_100per mb_10"></div></div> </li>
								<li>  <div class="shimmer"><div class="h_3 w_100per mb_5"></div></div> </li>
								<li>  <div class="shimmer"><div class="h_3 w_100per mb_10"></div></div> </li>
								<li>  <div class="shimmer"><div class="h_3 w_48per mb_10"></div></div> </li>
                                <li> 
                                <div class="shimmer"> 
                                    <div class="h_7 w_48per f_left"></div>
                                    <div class="h_7 w_48per f_right mb_10"></div>
                                </div> 
                                </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
							</ul>
						</div>
						<div class="procced-checkout">
							<ul>
								<li>  <div class="shimmer"><div class="h_3 w_100per mb_10"></div></div> </li>
							</ul>    
						</div>
					</div>
				</div>
			</div>

			</div>
		</div>
	</div>
	</section>



</div>

<div v-else>
<section id="cart-page-shimmer">
	<div class="container">
		<div class="row">
			<div v-if="cartData.sub_total == 0 || cartData.status == 0" class="col-md-12">
				<p> <img @error="imageLoadError" src="/images/no-item-in-cart.gif" alt="">  </p>
				<h4> {{ $t('No product in cart') }} ! </h4>
				<p> <router-link :to="{name:'products'}"> {{ $t('Continue shopping') }} </router-link></p>
			</div>
			<div v-else class="col-md-12 simar_lodding">
			
			<div class="container">
				<div class="row cart-page-container">
					<div class="col-12 col-sm-12 col-md-8 col-lg-9 pr-0">
						<div class="row shoping-cart-text">
							<div class="col-12 col-sm-12 col-md-12">

								<div class="shimmer">
									<div class="h_3 w_10 ml_10 mt_5"></div>
								</div>

							</div>
						</div>
						<div class="cart-calculation">
							<table  class="table text-left cart_table" width="100%">
								<thead>
									<tr>
										<th width="5%"> <div class="shimmer"><div class="h_2 w_5"></div></div> </th>
										<th width="60%"></th>
										<th width="5%"> </th>
										<th width="5%"> </th>
										<th width="20%">  <div class="shimmer"><div class="h_2 w_5"></div></div> </th>
										<th width="5%" style="text-align: right;">  <div class="shimmer"><div class="h_2 w_5"></div></div></th>
									</tr>
								</thead>
								<tbody>
									<tr class="group_header mb-3">
										<td colspan="7">
											<small><div class="shimmer"><div class="h_2 w_7"></div></div>  </small>
										</td>
									</tr> 
									<tr class="cart_product_group">
										<td> 
                                            <div class="product-cart-img">
                                                <div class="shimmer"><div class="h_10 w_6 mr_5"></div></div>  
                                            </div> 
                                        </td>
										<td> 
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                        </td>
										<td> </td>
										<td> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div></td>
									</tr>

									<tr class="group_header mb-3">
										<td colspan="7">
											<small><div class="shimmer"><div class="h_2 w_7"></div></div>  </small>
										</td>
									</tr> 
									<tr class="cart_product_group">
										<td> 
                                            <div class="product-cart-img">
                                                <div class="shimmer"><div class="h_10 w_6 mr_5"></div></div>  
                                            </div> 
                                        </td>
										<td> 
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                        </td>
										<td> </td>
										<td> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div></td>
									</tr>
									<tr class="group_header mb-3">
										<td colspan="7">
											<small><div class="shimmer"><div class="h_2 w_7"></div></div>  </small>
										</td>
									</tr> 
									<tr class="cart_product_group">
										<td> 
                                            <div class="product-cart-img">
                                                <div class="shimmer"><div class="h_10 w_6 mr_5"></div></div>  
                                            </div> 
                                        </td>
										<td> 
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                            <div class="shimmer"><div class="h_10 w_6 mr_5 f_left"></div></div>  
                                        </td>
										<td> </td>
										<td> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div> </td>
										<td> <div class="shimmer"><div class="h_3 w_5"></div></div></td>
									</tr>
								</tbody>
							</table>
						</div>
					</div>
					<div class="col-12 col-sm-12 col-md-4 col-lg-3 payment">
						<div class="note">
							<div class="shimmer"><div class="h_2 w_48per mb_5"></div></div>
						</div>
						<div class="payment-calculation">
							<ul>
								<li> <div class="shimmer"><div class="h_10 w_100per mb_5"></div></div> </li>
								<li> <div class="shimmer"><div class="h_7 w_100per mb_10"></div></div> </li>
								<li>  <div class="shimmer"><div class="h_3 w_100per mb_5"></div></div> </li>
								<li>  <div class="shimmer"><div class="h_3 w_100per mb_10"></div></div> </li>
								<li>  <div class="shimmer"><div class="h_3 w_48per mb_10"></div></div> </li>
                                <li> 
                                <div class="shimmer"> 
                                    <div class="h_7 w_48per f_left"></div>
                                    <div class="h_7 w_48per f_right mb_10"></div>
                                </div> 
                                </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
                                <li><div class="shimmer"><div class="h_2 w_100per mb_5"></div></div> </li>
							</ul>
						</div>
						<div class="procced-checkout">
							<ul>
								<li>  <div class="shimmer"><div class="h_3 w_100per mb_10"></div></div> </li>
							</ul>    
						</div>
					</div>
				</div>
			</div>

			</div>
		</div>
	</div>
</section>
</div>





    <div class="modal fade" id="addressModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered modal-lg" role="document">
        <div class="modal-content">
        <div class="modal-header border-bottom-0">
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
            </button>
        </div>
        <div class="modal-body">
            <div class="form-title text-center">
            <h5><b>{{ $t('Select Address from Address book') }}</b></h5>
            </div>
            <div class="d-flex flex-column text-center">

                <ul class="nav nav-tabs">
                    <li class="btn btn-dark active"><a data-toggle="tab" href="#home">{{ $t('Address book') }}</a></li>
                    <li class="btn btn-dark"><a data-toggle="tab" href="#menu1"> <i class="fa fa-plus"></i> {{ $t('Add new address') }}</a></li>
                </ul>

                <div class="tab-content">
                    <div id="home" class="tab-pane fade in active">
                            <table class="table table-hover">
                            <thead>
                                <tr>
                                    <th scope="col"> {{ $t('Full Name') }} </th>
                                    <th scope="col"> {{ $t('Phone') }}</th>
                                    <th scope="col"> {{ $t('Address') }}</th>
                                    <th scope="col"> {{ $t('Defalut') }}</th>
                                </tr>
                            </thead>
                            <tbody>
                              
                                <tr v-for="(address, index) in logged_in_user_address" :key="index" @click.prevent="change_address(address.id)">
                                    <td> {{ address.shipping_first_name }}  {{ address.shipping_last_name }}  </td>
                                    <td> {{ address.shipping_phone }} </td>
                                    <td>{{ address.division.title }}, {{ address.district.title }}, {{ address.upazila.title }}, {{ address.union.title }}</td>
                                    <td> 
                                        <span v-if="logged_in_user.default_address_id == address.id">
                                          <div class="select_address"> </div>
                                        </span>
                                     </td>
                                </tr>
                               
                            </tbody>
                            </table>
                    </div>
                    <div id="menu1" class="tab-pane fade">
                        <div class="col-md-12">
                                <form @submit.prevent="addNewAddress()">
                                <div class="options">
                                    <div class="row text-left">
                                        <div class="col-md-6">
                                            <div class="form-group">
                                                <label for=""> {{ $t('Full Name') }}<span style="color:#f00">*</span></label>
                                                <input type="text" class="form-control shipping_first_name" :placeholder="$t('Full Name')+'..'" required>
                                                <div class="validation_error" v-if="errors.shipping_first_name" v-html="errors.shipping_first_name[0]" />
                                            </div>
                                        </div>

                                       <div class="col-md-6">
                                            <div class="form-group">
                                                <label for="">  {{ $t('Phone') }} <span style="color:#f00">*</span></label>
                                                <input type="text" class="form-control popup_phone" :placeholder="$t('Phone')+'..'" required>
                                                <div class="validation_error" v-if="errors.shipping_phone" v-html="errors.shipping_phone[0]" />
                                            </div>
                                        </div>

                                        <div class="col-md-6">
                                                <div class="form-group">
                                                    <label for="">  {{ $t('Division') }}<span style="color:#f00">*</span></label>
                                                         
                                                        <select  @change.prevent="getDistrict()" name="division" id="division" class="form-control" required>
                                                                <option disabled selected>--Select Division--</option>
                                                                <option value="68" >Dhaka</option>
                                                                <option value="36">Chattogram</option>
                                                                <option value="60">Rajshahi</option>
                                                                <option value="65">Khulna</option>
                                                                <option value="66">Barishal</option>
                                                                <option value="67">Sylhet</option>
                                                                <option value="69">Rangpur</option>
                                                                <option value="6175">Mymensingh</option>
                                                        </select>
                                                        <div class="validation_error" v-if="errors.shipping_division" v-html="errors.shipping_division[0]" />
                                                        
                                                </div>
                                        </div>
                                        <div class="col-md-6">
                                                <div class="form-group">
                                                    <label for=""> {{ $t('District') }} <span style="color:#f00">*</span></label>
                                                        <select  @change.prevent="getUpazila()" name="district" id="district" class="form-control" required>
                                                                <option disabled selected>--Select District--</option>
                                                                <option data-removeable="true" v-for="(district,index) in districts" :key="index" :value="district.id">{{district.title}}</option>
                                                        </select>
                                                        <div class="validation_error" v-if="errors.shipping_district" v-html="errors.shipping_district[0]" />
                                                </div>
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="col-md-6">
                                                <div class="form-group">
                                                    <label for="">{{ $t('Upazila / Thana') }} <span style="color:#f00">*</span></label>
                                                        <select  @change.prevent="getUnion()" name="upazila" id="upazila" class="form-control" required>
                                                                <option disabled selected>--Select Upazila--</option>
                                                                <option data-removeable="true" v-for="(upazila,index) in upazilas" :key="index" :value="upazila.id">{{upazila.title}}</option>
                                                        </select>
                                                        <div class="validation_error" v-if="errors.shipping_thana" v-html="errors.shipping_thana[0]" />
                                                </div>
                                        </div>
                                        <div class="col-md-6">
                                                <div class="form-group">
                                                    <label for=""> {{ $t('Union / Area') }}<span style="color:#f00">*</span></label>
                                                        <select name="union" id="union" class="form-control" required>
                                                                <option disabled selected>--Select Union--</option>
                                                                <option data-removeable="true" v-for="(union,index) in unions" :key="index" :value="union.id">{{union.title}}</option>
                                                        </select>
                                                        <div class="validation_error" v-if="errors.shipping_union" v-html="errors.shipping_union[0]" />
                                                </div>
                                        </div>
                                        <div class="col-md-6">
                                                <div class="form-group">
                                                    <label for="">{{ $t('Post code') }}<span style="color:#f00">*</span></label>
                                                   <input type="text" class="form-control popup_post_code" :placeholder="$t('Post code')+'..'" required>
                                                   <div class="validation_error" v-if="errors.shipping_postcode" v-html="errors.shipping_postcode[0]" />
                                                </div>
                                        </div>

                                        <div class="col-md-6">
                                            <div class="form-group">
                                                <label for="">  {{ $t('Email') }}</label>
                                                <input type="text" class="form-control shipping_email" :placeholder="$t('Email')+'..'" >
                                            </div>
                                        </div>
                                        <div class="col-md-12">
                                            <div class="form-group">
                                                <label for="">  {{ $t('Address') }} <span style="color:#f00">*</span></label>
                                                <textarea name="" id="" cols="30" rows="3" class="form-control shipping_address" :placeholder="$t('Address')+'..'" required></textarea>
                                                <div class="validation_error" v-if="errors.shipping_address" v-html="errors.shipping_address[0]" />
                                            </div>
                                        </div>
                                    </div>
                                    <p class="text-right"> <button type="submit" class="btn btn-dark"> {{ $t('Add new address') }}</button> </p>
                                </div>
                                </form>


                            </div>
                    </div>
                </div>
            </div>
        </div>
        </div>
    </div>
    </div>


<div v-if="useableVouchers.length" class="modal fade use_voucher_modal" tabindex="-1" role="dialog" aria-labelledby="myLargeModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
        
        <form @submit.prevent="checkUseableVoucher">
            <div class="modal-header border-bottom-0 abs_right ">
                <button type="button" class="close custom_close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
                </button>
            </div>
        
            <div class="modal-body">
                <h3 class="text-center"> {{ $t('Use Voucher') }} </h3>
               
                <p class="text-center"> {{ $t('Please select the voucher') }}</p>
                <div class="row">
                    <div  class="col-md-6" v-for="(cv,index) in useableVouchers" :key="index">
                        <p><input type="radio" name="useable_vouchers" class="useable_vouchers_radio" :data-voucher-discount="Number(cv.voucher.amount)" :value="cv.voucher_id" required> <img @error="imageLoadError" style="width:80%" :src="baseurl+'/'+cv.voucher.banner" alt=""></p>
                    </div>
                </div>
            </div>

            <div class="modal-footer border-top-0 ">
                <p class="text-right"><button type="submit" class="btn btn-dark"> {{ $t('Apply') }} </button></p>
            </div>
        </form>

    </div>
  </div>
</div>




</div>
</template>


<script>
import axios from 'axios'

export default {
	data(){
		return{
            loading:false,
			carts:'',
			baseurl:'',
			sub_total:'',
			agree:false,
			note:'',
			coupon_discount:{},
			cartCount:'',
            addresses: {},
            districts:{},
            upazilas:{},
            unions:{},
            errors:{},
            errors: [],
            finalCalculatedTotal:0,
		}
	},

	methods:{
		loading_method(){
		 this.loading = true;
		},
        proceedToPay(){
            let collectedVoucher = '';
            let usedVoucher = '';
			let token = localStorage.getItem("token");
			let axiosConfig = {
			  headers: {
				  'Content-Type': 'application/json;charset=UTF-8',
				  "Access-Control-Allow-Origin": "*",
				  'Authorization': 'Bearer '+token
			  }
			}

            if(jQuery('.required_addtess').attr('data-required-address') == 'true'){
                swal ( "Oops" , 'Please select your default shipping address!',  "error");
                return true;
            }

            if(this.useableVouchers.length){
                let isCheckedUsedVoucher = false;
                jQuery('.useable_vouchers_radio').each(function(key,val){
                    if (jQuery(this).is(':checked')) {
                        isCheckedUsedVoucher = true;
                        usedVoucher = jQuery(this).val();
                    }
                });
                if(!isCheckedUsedVoucher){
                    swal ( "Oops" , 'Please select which voucher you want to use!',  "error");
                    jQuery('.voucher_button li').addClass('validated_class');
                    return true;
                }
            }

            if (jQuery('.agree').is(':checked')) {

                    let shipping_method  = [];

                    jQuery('.select_shipping_options .selected_shipping').each(function(key,val){
                        shipping_method[key] = { 
                            product_id : jQuery(this).attr('data-product-id'),
                            shipping_method: jQuery(this).attr('data-shipping-method'),
                            shipping_cost: jQuery(this).attr('data-shipping-cost'),
                        }
                    });

                    let formData = {
                        note: jQuery('.form_note').val(),
                        coupon: jQuery('#couponeCode').val(),
                        collectedVoucher: collectedVoucher,
                        usedVoucher: usedVoucher,
                        payment_method : jQuery('.paymentmethod .selected_payment').attr('data-payment-method'),
                        shipping_method : shipping_method
                    }
     
                    
                    $('.proceed_to_pay').attr('disabled', true);
                    $('.proceed_to_pay').html('<span class="spinner-border spinner-border-sm"></span>');
                    

                    axios.post(this.$baseUrl+'/api/v1/order', formData, axiosConfig).then(response => {
                        if(response.data.status == 1){
                            swal({
                                title: 'Your order has been placed successfully.',
                                icon: "success",
                                timer: 3000
                            }).then(()=>{
                                $('.proceed_to_pay').attr('disabled', false);
                                $('.proceed_to_pay').html('Proceed To Pay');
                                this.$store.dispatch('loadedCart');
                                this.$router.push({name:'orderDetails',params: {id: response.data.invoice.order_id } });
                            });
                        }else if( response.data.status == 302){
                            window.location.href = response.data.url;
                        }else{
                            swal ( "Oops" , 'Order Failed! Please try again later',  "error");
                        }
                    });
            }else{
                swal ( "Oops" , 'Please accept terms and conditions!',  "error");
            }
        },

        checkUseableVoucher(){
            let usedVoucher = 0;
            jQuery('.close').trigger('click');
             jQuery('.useable_vouchers_radio').each(function(key,val){
                if (jQuery(this).is(':checked')) {
                    usedVoucher = jQuery(this).attr('data-voucher-discount');
                }
            });
            jQuery('.show_voucher_discount').attr('data-voucher-discount',Number(usedVoucher));
            jQuery('.show_voucher_discount .v_amount').html('BDT '+usedVoucher);
            this.calculateFinalAmount();
        },

        calculateFinalAmount(){
                let subTotal = Number(jQuery('.data_sub_total').attr('data-subtotal-amount'));
                let shippingCost = Number(jQuery('.shipping_cost_li').attr('data-shipping-cost'));
                let couponAmount = jQuery('.coupon_discount').attr('data-coupon-discount') ? Number(jQuery('.coupon_discount').attr('data-coupon-discount')) : 0;
                let voucherAmount = jQuery('.show_voucher_discount').attr('data-voucher-discount') ? Number(jQuery('.show_voucher_discount').attr('data-voucher-discount')) : 0;
                //console.log('subTotal :'+subTotal+',couponAmount:'+couponAmount+',voucherAmount:'+voucherAmount+',shippingCost: '+shippingCost);
                this.finalCalculatedTotal = (subTotal+shippingCost) - (couponAmount+voucherAmount);
                $('.calculatedTotal').text(this.finalCalculatedTotal);

        },
        imageLoadError(event){
			event.target.src = "/images/notfound.png";
		},
        addNewAddress(){
            let token = localStorage.getItem("token");
            let axiosConfig = {
                headers: {
                    'Content-Type': 'application/json;charset=UTF-8',
                    "Access-Control-Allow-Origin": "*",
                    'Authorization': 'Bearer '+token
                }
            }
            let formData = new FormData();
            formData.append('shipping_first_name', $('.shipping_first_name').val());
            formData.append('shipping_last_name', $('.shipping_last_name').val());
            formData.append('shipping_division', $('#division').find('option:selected').val());
            formData.append('shipping_district', $('#district').find('option:selected').val());
            formData.append('shipping_thana', $('#upazila').find('option:selected').val());
            formData.append('shipping_union', $('#union').find('option:selected').val());
            formData.append('shipping_postcode', $('.popup_post_code').val());
            formData.append('shipping_phone', $('.popup_phone').val());
            formData.append('shipping_email', $('.shipping_email').val());
            formData.append('shipping_address', $('.shipping_address').val());

            axios.post(this.$baseUrl+'/api/v1/add-new-address', formData, axiosConfig).then(response => {
                if(response.data.status == 1){
                    swal({
                        title: 'New address added successfull.',
                        icon: "success",
                        timer: 3000
                    });
                    this.$store.dispatch('loadedUser');
                    jQuery('a[href="#home"]').trigger('click');
                }else{
                    this.errors = response.data.message;
                }
            });
        },
        change_address($address_id){
			let token = localStorage.getItem("token");
			let axiosConfig = {
				headers: {
					'Content-Type': 'application/json;charset=UTF-8',
					"Access-Control-Allow-Origin": "*",
					'Authorization': 'Bearer '+token
				}
			}
			let formData = new FormData();
			formData.append('address_id', $address_id);
            axios.post(this.$baseUrl+'/api/v1/update-default-address', formData, axiosConfig).then(response => {
				if(response.data.status == 1){
                    this.$store.dispatch('loadedCart');
                    this.$store.dispatch('loadedUser');
                    jQuery('.close').trigger('click');   
               }else{
                    swal ( "Please check" ,  response.data.message,  "error");
                }
			});
        },
    


        async getDistrict(){
        let id =  jQuery('#division').find('option:selected').val();
        await axios.get(this.$baseUrl + "/api/v1/get-district/"+id).then((response) => {
                this.upazilas = {};
                this.unions = {};
                this.districts = response.data;
            });
        },
        
        async getUpazila(){
            let id =  jQuery('#district').find('option:selected').val();
            await axios.get(this.$baseUrl + "/api/v1/get-upazila/"+id).then((response) => {
                    this.unions = {};
                    this.upazilas = response.data;
                });
            },
            async getUnion(){
                let id =  jQuery('#upazila').find('option:selected').val();
            await axios.get(this.$baseUrl + "/api/v1/get-union/"+id).then((response) => {
                    this.unions = response.data;
                });
        },


		applyCouponCode(){
            let token = localStorage.getItem("token");
			let axiosConfig = {
				headers: {
					'Content-Type': 'application/json;charset=UTF-8',
					"Access-Control-Allow-Origin": "*",
					'Authorization': 'Bearer '+token
				}
			}

			let formData = new FormData();
			formData.append('coupon', $('#couponeCode').val());
            axios.post(this.$baseUrl+'/api/v1/get-coupon-amount', formData,axiosConfig).then(response => {
                if(response.data.status == 1){
                    this.coupon_discount = response.data;
                    jQuery('.coupon_discount').attr('data-coupon-discount',response.data.amount);

                    jQuery('#addCouponBlock').hide();
                    jQuery('.coupon_discount').show();
                    
                      this.calculateFinalAmount();
                    swal({
						title: response.data.message,
						icon: "success",
						timer: 3000
					});
                }else{
                    swal ( "Oops", response.data.message, "error");
                }
				
			});
		},
        removeCoupon(){
            jQuery('#addCouponBlock').show();
            jQuery('.coupon_discount').hide();
            jQuery('.coupon_discount').attr('data-coupon-discount',0)
            let that = this;
            setTimeout(function(){ 
                that.calculateFinalAmount();
             },200);
        },

		updateShippingOption(shipping_method, shipping_cost, rowId){
			let token = localStorage.getItem("token");
			let axiosConfig = {
				headers: {
					'Content-Type': 'application/json;charset=UTF-8',
					"Access-Control-Allow-Origin": "*",
					'Authorization': 'Bearer '+token
				}
			}
			let formData = new FormData();
			formData.append('shipping_method', shipping_method);
			formData.append('shipping_cost', shipping_cost);
			formData.append('rowId', rowId);
			axios.post(this.$baseUrl+'/api/v1/update-shipping-option', formData,axiosConfig).then(response =>{
				if(response.data.status == '1'){
					this.$store.dispatch('loadedCart');
					swal({
						title: "Shipping method updated Successfully.",
						icon: "success",
						timer: 3000
					});
				}else{
					swal ( "Oops", response.data.message, "error");
				}

			});
		},
    	scrollToTop() {
            window.scrollTo(0,0);
        }

        // getCollectedVoucher(){
		// 	let token = localStorage.getItem("token");
		// 		let axiosConfig = {
		// 			headers: {
		// 				'Content-Type': 'application/json;charset=UTF-8',
		// 				"Access-Control-Allow-Origin": "*",
		// 				'Authorization': 'Bearer '+token
		// 			}
		// 		}

		// 		axios.get(this.$baseUrl + "/api/v1/get-collected-vouchers",axiosConfig).then((response) => {
        //             this.collectedVoucher = response.data
		// 		});
        // },
        // getUseableVouchers(){
        //     let token = localStorage.getItem("token"); 
        //     let axiosConfig = {
        //         headers: {
        //             'Content-Type': 'application/json;charset=UTF-8',
        //             "Access-Control-Allow-Origin": "*",
        //             'Authorization': 'Bearer '+token
        //         }
        //     }
        //     axios.get(this.$baseUrl + "/api/v1/get-useable-vouchers",axiosConfig).then((response) => {
        //         this.useableVouchers = response.data
        //     });
        // },

	},
	computed:{
        collectedVoucher(){
            return this.$store.getters.getLoadedVocher;
        },
        useableVouchers(){
            return this.$store.getters.getLoadedUseableVocher;
        },


		cartData(){
		  this.sub_total = this.$store.getters.getLoadedCart.sub_total;
          this.finalCalculatedTotal =  this.sub_total+this.$store.getters.getLoadedCart.shipping_cost
		  return this.$store.getters.getLoadedCart;
		},
		user(){
		  return this.$store.getters.getLoadedUser.id;
		},
        logged_in_user(){
            return this.$store.getters.getLoadedUser.user;
        },
        logged_in_user_address(){
            let x = this.$store.getters.getLoadedUser.address;
            let res = 0;
            if(x != undefined){
                if(x.length != 0){
                    res = this.$store.getters.getLoadedUser.address;
                }
            }
            return res;
        }
	},

	mounted(){
        this.scrollToTop();
		const plugin = document.createElement("script");
		plugin.setAttribute( "src",this.$frontendUrl+"/assets/js/parts/cart.js");
		plugin.async = true;
		document.body.appendChild(plugin);
		this.baseurl = this.$baseUrl;
        document.title = "Dhroobo | Checkout"; 
        // this.getCollectedVoucher();
        // this.getUseableVouchers();
        this.$store.dispatch('loadedVoucher');
        this.$store.dispatch('loadedUsableVoucher');
        this.loading_method();
	}
  
}
</script>





