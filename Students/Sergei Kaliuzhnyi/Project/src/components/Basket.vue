<template>
    <div class="header__cart-drop">
        <div class="header__drop">
            <Item 
                v-for="item of items" 
                :key="item.productId"
                :item="item"
                typeOfItem="basket"
            />
            <div class="header__dropcost">
                <p>total</p>
                <p>{{ getSum }}</p>
            </div>
            <a href="checkout.html" class="header__dropbutton">Checkout</a>
            <a href="shopping_cart.html" class="header__dropbutton">Go to cart</a>                      
        </div>
    </div>
</template>

<script>
import Item from './Item.vue'
import { get, post, put, del } from '../libraries/requests';
// import Item from './item.vue'
export default {
    components: { Item },
    data() {
        return {
            items: [],
            url: '/api/basket'
        }
    },
    computed:{
        getSum(){
            let result = 0;
            this.items.map (el => result += (el.productPrice * el.amount));
            return result;
        }
    },
    methods: {
        add(item) {
            let find = this.items.find(el => el.productId == item.productId);
            if (find) {
                put(`/api/basket/${find.productId}`, { amount: 1 })
                .then(status => {
                    if (status.status) {
                        find.amount++;
                    }
                })
            } else {
                let newItem = Object.assign({}, item, { amount: 1 });
                post('/api/basket', newItem)
                .then(status => {
                    if (status.status) {
                        this.items.push(newItem);
                    }
                })
            }
        },
    
        remove(id) {
            let find = this.items.find(el => el.productId == id);
            if (find.amount > 1) {
                put(`/api/basket/${id}`, { amount: -1 })
                .then(status => {
                    if (status.status) {
                        find.amount--;
                    }
                })
            } else {
                del(`/api/basket/${id}`)
                .then(status => {       /*{ status: true }*/
                    if (status.status) {
                        this.items.splice(this.items.indexOf(find), 1);
                    }
                })
            }
        }
    },
    mounted() {
        get(this.url).then(basket => { this.items = basket.content });
    }
}
</script>

<style>

</style>