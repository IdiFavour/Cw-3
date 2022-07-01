<script>
export default{
    props: {
        cart: Array
    },
    data(){
        return {
            user_order: {
                firstName: "",
                lastName: "",
                phoneNumber: "" 
            }  
        }
    },
    methods: {
        checkOut(){
            this.$emit('checkout', this.user_order)
            this.clearForm()
        },
        removeCartItem(){
            this.$emit('remove-lesson', {id: id, stock: stock})
        },
        clearForm(){
            this.user_order.firstName =""
            this.user_order.lastName =""
            this.user_order.phoneNumber=""

        }
    }
}
</script>

<template>
<div class="row">
    <div class="col-lg-6">
        <div class="still">
            <h2 class="mt-5">Checkout</h2>
            <div class="row">
                <div class="col-6">
                    <div class="form-outline mb-3 border mt-4">
                        <input type="text" id="form5Example1" v-model="user_order.firstName" class="form-control" />
                        <label class="form-label" for="form5Example1">Firstname</label>
                    </div>
                </div>
                <div class="col-6">
                    <div class="form-outline mb-3 border mt-4">
                        <input type="text" id="form5Example1" v-model="user_order.lastName" class="form-control" />
                        <label class="form-label" for="form5Example1">Lastname</label>
                    </div>

                </div>
                <div class="col-6">
                    <div class="form-outline mb-4 border">
                        <input type="number" id="form5Example2" v-model="user_order.phoneNumber" class="form-control" />
                        <label class="form-label" for="form5Example2">Phone Number</label>
                    </div>
                </div>
            </div>
            <br>
            <button class="btn main-color text-white mb-4" v-on:click="checkOut">Place Order</button>
        </div>

    </div>
    <div class="col-lg-6">
        <div v-for="lesson in cart" class="col-lg-12 p-2" :key="lesson">
            <div class="p-2 border ">
                <div class="d-flex justify-content-between align-items-center">
                    <div class="mt-2">
                        <h4 v-text="lesson.subject"></h4>
                        <div class="mt-4">
                            <h5 class=" mb-0" v-text="lesson.location"></h5>
                            <p class="mb-0">Price: ${{lesson.price}}</p>
                            <p class="mb-0">Amount: ${{lesson.stock}}</p>

                            <button class="btn btn-danger" v-on:click="removeCartItem(lesson.id, lesson.stock)"><small>Remove</small></button>
                        </div>
                    </div>
                    <div class="image">
                        <figure class="mb-0">
                            <img v-bind:src="lesson.image" alt="" width="120" height="140">
                        </figure>
                    </div>
                </div>
            </div>
        </div>
        <div v-if="cart == ''">
            <div class="text-center mt-4">
                <img src="src/assets/images/empty-box.png" alt="" width="160" height="160">
                <p class="mt-2"><strong>Cart Empty</strong></p>
            </div>
        </div>
    </div>
</div>
</template>