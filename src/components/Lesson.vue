<script>
export default{
    props: {
        lesson: Object
    },
    methods: {
        addToCart(){
            this.$emit('add-to-cart', this.lesson)
        }
    }
}
</script>

<template>
<div class=" p-2 border">
    <div class="card-img text-center p-2 bg-dark-white">
        <img v-bind:src="lesson.image" alt="" width="100" height="100">
    </div>
    <div class="card-body px-0 py-2">
        <small class="text-black-50">Stock {{lesson.stock > 0 ?
            lesson.stock : "Out of Stock"}}</small>
        <h5 class="mb-0" v-text="lesson.subject"></h5>
        <p class="mb-0" v-text="lesson.location"></p>
        <div class="row">
            <div class="col-6">
                <p class="mb-0 "><small class="text-black-50">Price:</small>
                    <strong>${{lesson.price}}</strong>
                </p>
            </div>
            <div class="col-6 d-flex justify-content-end">
                <button type="button" class="btn main-color btn-floating" v-on:click="addToCart(lesson)" :disabled="lesson.stock < 1">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24"
                        style="fill: rgb(238, 237, 237);">
                        <path
                            d="M5 22h14c1.103 0 2-.897 2-2V9a1 1 0 0 0-1-1h-3V7c0-2.757-2.243-5-5-5S7 4.243 7 7v1H4a1 1 0 0 0-1 1v11c0 1.103.897 2 2 2zM9 7c0-1.654 1.346-3 3-3s3 1.346 3 3v1H9V7zm-4 3h2v2h2v-2h6v2h2v-2h2l.002 10H5V10z">
                        </path>
                    </svg>
                </button>
            </div>
        </div>
    </div>

</div>
</template>