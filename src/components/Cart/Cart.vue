<template>
    <div class="container">    

    <table class="table table-hover table-condensed" id="cart">
    <thead>
        <th>Product</th>
        <th>Price</th>
        <th>Quantity</th>
        <th>Subtotal</th>
    </thead>
    <tr v-if="products.length<=0">
        <!-- <td colspan="4">No Items in cart</td> -->
        <td colspan="6">
            <img src="../../assets/cart-img.png" style="height: 400px;"/>
            <tr>
                <td colspan="6">
                <center><button 
                    class="btn btn-success"
                    @click="redirectHome">
                    Continue Shopping
                </button></center>
                </td>
            </tr>
        </td>
    </tr>
    <tr v-else v-for="(c, id) in products" :key="id">
		<td data-th="Product">
			<div class="row" >
				<div class="col-sm-2 hidden-xs">
					<img :src="thumbUrl+c.prod_img" alt="..." class="img-responsive" style="margin-left: 25px;"/>
				</div>
				<div class="col-sm-10" id="pname-css">
					<h4 class="nomargin">{{ c.prod_name }}</h4>					
				</div>
			</div>
		</td>
		<td data-th="Price">{{ formatPrice(c.prod_price) }}</td>
		<td data-th="Quantity">
			<input type="number" class="form-control text-center" 
                v-model="c.prod_qty"
				value="1" 
				min="1"
                :id="c.prod_qty"
                @input="changeQty(c.prod_id)"
            >
		</td>
		<td data-th="Subtotal" class="text-center">{{subtotal(c.prod_qty, c.prod_price)}}</td>
		<td class="actions" data-th="">
			<button class="btn btn-danger btn-sm" @click="removeItem(c.prod_id)"><i class="fa fa-trash-o"></i></button>								
		</td>
	</tr>
    <tr data-v-32c58de7="" v-if="products.length>0">
        <td data-v-32c58de7="">
            <button 
                data-v-32c58de7="" 
                class="btn btn-warning"
                @click="redirectHome">
            Save and Continue Shopping
            </button>
        </td>
        <td data-v-32c58de7="" colspan="2" class="hidden-xs"></td> 
        <td data-v-32c58de7="" class="hidden-xs text-center">
            <strong data-v-32c58de7="">Total ₹ {{ total }}</strong>
        </td> 
        <td data-v-32c58de7="">
            <button 
                data-v-32c58de7="" 
                class="btn btn-success btn-block"
                @click="checkoutClick"
            >
            Checkout
            </button>
        </td>
    </tr>
    </table>

    <login v-if="showLogin" :toggle="checkoutClick"/>
    </div>
</template>

<script>

import Login from '../../components/Login/Login.vue';
export default {
    components: {
        Login
    },
    data () {
        return {
            local: [],
            cartItems: [],
            cartDet: [],
            qty: [],
            showLogin: false
        }
    },

    created () {
        this.$store.dispatch('getCartProducts');
        //this.$store.dispatch('getCartProducts');
    },
    
    mounted () {
       
        this.local = JSON.parse(localStorage.getItem('product_id'));
        if(this.local!=null) {            
            this.cartItems = this.local.map((id) => {
                return id;
            });
        }
    },

    computed: {
        products () {
            return this.$store.state.CartStore.productsDtl;
        },
        // products () {    
        //     this.cartDet = [];
        //     this.$store.state.CartStore.productsDtl.map((pro) => {
        //         this.cartItems.map((c) => {
        //             if(c.product_id===pro.product_id) {
        //                 this.cartDet.push(pro);
        //             }
        //         })
        //     })            
        //     return this.cartDet;            
        // },
        thumbUrl () {
            return this.$store.state.AppStore.thumbUrl;
        },
        total () {
            return this.products.reduce((total, p) => {
                return total + p.prod_price * p.prod_qty
            }, 0)
        }
    },

    methods: {
        redirectHome () {
            this.$router.push('/');
        },
        removeItem (product_id) {
            
            this.cartItems = this.cartItems.filter((item, i) => {            
                return (parseInt(item.product_id)!==parseInt(product_id))                 
            })
            
            localStorage.setItem('product_id', JSON.stringify(this.cartItems));
            this.$store.dispatch('addToCart');
            this.$store.dispatch('removeProducts', product_id);
        },
        formatPrice (price) {
            let pprice = price.toFixed(2).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1,")
            return "₹ " + pprice.split('.')[0];
        },
        subtotal (qty, price) {
            let value = price*qty;
            return "₹ " + (value.toFixed(2).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1,")).split('.')[0]
        },
        changeQty (pid) {
            let id = pid;
            let qty = event.target.value ;
    
            this.$store.dispatch('updateQty', {
                id,
                qty
            });
        },
        checkoutClick () {
            if(localStorage.getItem('token') && localStorage.getItem('token')!=null) {

            }
            else {
                this.showLogin = !this.showLogin; 
            }
        }
    }
}
</script>

<style scoped>
    table {
        width: 100%;
        max-width: 100%;
        margin-bottom: 20px;
        border-spacing: 0;
        border-collapse: collapse;
        background-color: transparent;
        display: table;
        box-sizing: border-box;
        margin-top: 50px;
        box-shadow: 4px 2px 25px 5px;
        color: gray;
    }
    thead {
        display: table-header-group;
        vertical-align: middle;
        border-color: inherit;
        border-bottom: 2px solid #ddd;
    }
    td {
        box-sizing: border-box;
        border-bottom: 2px solid #ddd;
        padding: 5px;
        line-height: 1.42857143;
    }
    th {
        font-weight: bold;
        display: table-cell;
        padding: 20px;
    }
    tr :-moz-last-node :last-child {
        border-bottom: none;
    }
    .btn-warning {
        display: inline-block;
        padding: 6px 12px;
        margin-bottom: 0;
        font-size: 14px;
        font-weight: 400;
        line-height: 1.42857143;
        text-align: center;
        white-space: nowrap;
        vertical-align: middle;
        -ms-touch-action: manipulation;
        touch-action: manipulation;
        cursor: pointer;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        background-image: none;
        border: 1px solid transparent;
        border-radius: 4px;
        float: left;
    }
    .btn-success {
        display: inline-block;
        /* padding: 6px 12px; */
        margin-bottom: 0;
        font-size: 14px;
        font-weight: 400;
        line-height: 1.42857143;
        text-align: center;
        white-space: nowrap;
        vertical-align: middle;
        -ms-touch-action: manipulation;
        touch-action: manipulation;
        cursor: pointer;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        background-image: none;
        border: 1px solid transparent;
        border-radius: 4px;
        float: right;
    }
    #pname-css {
        display: flex;
        max-height: 200px;
        height: 100px;
        justify-content: center;
        align-items: center;
    }
   
</style>

