<script>
import Lesson from "./components/Lesson.vue"
import Checkout from "./components/Checkout.vue"

export default{
  components: {
    Lesson, Checkout
  },
  data(){
    return{
        showProduct: true,
        lowHigh: 'Ascending',
        lessons: [],
        cart: [],
        searchInput: "",
        sortBy: '',

    }
  },
  methods: {
    getLessons: function () {
      fetch("https://idifavour-cst2.herokuapp.com/collection/lessons")
          .then(res => {
              return res.json()
          })
          .then(data => {
              this.lessons = data
          })

          .catch(err => {
              lessons = "unable to get lessons data"
              console.log("unable to get lessons list")
          })
    },
    showCheckout() {
        this.showProduct = this.showProduct ? false : true;
    },
    addToCart(lesson) {
        if (lesson.stock >= 1) {
            let InCart = false
            if (this.cartCount() >= 1) {
                for (let i = 0; i < this.cart.length; i++) {
                    if (this.cart[i].id == lesson._id) {
                        this.cart[i].stock += 1
                        InCart = true
                        break
                    }
                }
                if (InCart == false) {
                    let item = {}
                    item.id = lesson._id
                    item.stock = 1
                    this.cart.push(item)
                }
            }
            else {
                let item = {}
                item.id = lesson._id
                item.stock = 1
                this.cart.push(item)
            }
            lesson.stock -= 1
        }
        else {
            lesson.stock = 0
        }
        console.log(this.cart)
    },
    cartCount() {
        return this.cart.length
    },
    checkOutItems() {
        let cartInfo = []
        for (let i = 0; i < this.cart.length; i++) {
            for (let k = 0; k < this.lessons.length; k++) {
                if (this.lessons[k]._id == this.cart[i].id) {
                    let item = {}
                    item.id = this.cart[i].id
                    item.title = this.lessons[k].title
                    item.location = this.lessons[k].location
                    item.price = this.lessons[k].price
                    item.images = this.lessons[k].images
                    item.stock = this.cart[i].stock
                    cartInfo.push(item)
                }
            }
        }
        return cartInfo
    },
    removeCartItem(id, stock) {
        let showcart = this.cart
        let less = this.lessons
        for (let i = 0; i < showcart.length; i++) {
            console.log(showcart[i].id)
            if (id == showcart[i].id) {
                showcart.splice(i, 1)

            }
        }
        for (let i = 0; i < less.length; i++) {
            console.log(less[i].id)
            if (id == less[i]._id) {
                less[i].stock += stock

            }
        }
    },
    checkOut(user_order){
      let order = {
          name: user_order.firstName +' '+user_order.lastName,
          phone_number: user_order.phoneNumber,
          items: this.cart
      }
      let order_string = (JSON.stringify(order))
      fetch('https://idifavour-cst2.herokuapp.com/collection/orders', {
          method: "POST",
          body: order_string,
          headers: {
              "Content-type": "application/json; charset=UTF-8"
          }
      })
      .then(response => response.json())
      .then(json_response => {
          console.log(json_response)
          this.placeOrder()
      })
      .catch(err => console.log(err))
    },
    placeOrder() {
        let stockNew = []
        let showcart = this.cart
        
        for (let i = 0; i < showcart.length; i++) {
            for (let j = 0; j < this.lessons.length; j++) {
                if (showcart[i].id == this.lessons[j]._id) {
                    let item = {
                        id: showcart[i].id,
                        stock: this.lessons[j].stock
                    }
                    stockNew.push(item)
                }
            }
        }
        let stockString = (JSON.stringify(stockNew))
        fetch('https://idifavour-cst2.herokuapp.com/collection/lessons', {
            method: "PUT",
            body: stockString,
            headers: {
                "Content-type": "application/json; charset=UTF-8"
            }
        })
            .then(res => res.json())
            .then(res => {
                console.log(res)
                Swal.fire(
                    'Success!',
                    'Order submitted successfully!',
                    'success'
                )
                showcart.splice(0, showcart.length)
            })
            .catch(err => console.log(err))
        if (this.firstName == '' && this.lastName == '' && this.phoneNumber == '' && this.cart.length == 0) {
            Swal.fire(
                'Error!',
                'Fill all details!',
                'Error'
            )
        }
        
    },
    searchFilter: function () {
      fetch(`https://idifavour-cst2.herokuapp.com/collection/lessons/search?filter=${this.searchInput}`)
          .then(res => {
              return res.json()
          })
          .then(data => {
              this.lessons = data
          })
          .catch(err => {
              this.lessons = []
              console.log(`unable to get lessons: ${err}`)
          })
    },
    sortByPrice: function (priceArray) {
        function compare(a, b) {
            if (a.price > b.price)
                return 1;
            if (a.price < b.price)
                return -1;
            return 0;
        }
        return priceArray.sort(compare);
    },
    sortBySubject: function (subjectArray) {
        function compare(a, b) {
            if (a.subject > b.subject)
                return 1;
            if (a.subject < b.subject)
                return -1;
            return 0;
        }
        return subjectArray.sort(compare);
    },
    sortByLocation: function (locationArray) {
        function compare(a, b) {
            if (a.location > b.location)
                return 1;
            if (a.location < b.location)
                return -1;
            return 0;
        }
        return locationArray.sort(compare);
    },
    sortByAva: function (avaArray) {
        function compare(a, b) {
            if (a.stock > b.stock)
                return 1;
            if (a.stock < b.stock)
                return -1;
            return 0;
        }
        return avaArray.sort(compare);
    },

    filterLessons: function () {
        let tempLessons = this.lessons

        // tempLessons = tempLessons.filter((lesson) => {
        //     return lesson.subject.toLowerCase().match(this.searchInput.toLowerCase()) || lesson.location.toLowerCase().match(this.searchInput.toLowerCase())
        // })
        if (this.sortBy == 'price') {
            tempLessons = this.sortByPrice(tempLessons)
        }
        else if (this.sortBy == 'subject') {
            tempLessons = this.sortBySubject(tempLessons)
        }
        else if (this.sortBy == 'location') {
            tempLessons = this.sortByLocation(tempLessons)
        }
        else if (this.sortBy == 'stock') {
            tempLessons = this.sortByAva(tempLessons)
        }
        else{
            tempLessons = this.sortBySubject(tempLessons)
        }

        if (this.lowHigh == 'Ascending') {
            return tempLessons
        }
        else if (this.lowHigh == 'Descending') {
            return tempLessons.reverse()
        }
        return tempLessons
    }

  },
  computed: {
    cartItemCount: function () {
        return this.cart.length
    },
  },
  created() {
      this.getLessons()
  }
  
}
</script>

<template>
  <nav class="navbar fixed-top navbar-expand-lg navbar-light shadow bg-light container-fluid">
      <!-- Container wrapper -->
      <div class="container-fluid">
          <!-- Toggle button -->
          <button class="navbar-toggler" type="button" data-mdb-toggle="collapse" data-mdb-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
              <i class="fas fa-bars"></i>
          </button>

          <!-- Collapsible wrapper -->
          <div class="collapse navbar-collapse" id="navbarSupportedContent">
              <!-- Navbar brand -->

              <!-- Left links -->
              <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                  <li class="nav-item">
                      <h4 class="nav-link h5 text-black">CST 3145</h4>
                  </li>
              </ul>
              <!-- Left links -->
          </div>
          <!-- Collapsible wrapper -->

          <!-- Right elements -->
          <div class="d-flex align-items-center">
              <!-- Icon -->
              <a class="text-reset me-3" v-on:click="showCheckout">
                  <i class="fas fa-shopping-cart fs-4"></i>
                  <span class="badge rounded-pill badge-notification bg-danger">{{cartItemCount}}</span>
              </a>

          </div>
          <!-- Right elements -->
      </div>
      <!-- Container wrapper -->
  </nav>
  <div class="container mt-5">
            <div v-if="showProduct">
                <div class="row pt-5">
                    <h4></h4>
                    <div class="col-lg-2">
                        <div class="still">
                            <div class="form-check">
                                <input class="form-check-input" value="Ascending" type="radio" id="ascending" v-model="lowHigh" />
                                <label class="form-check-label" for="ascending">Ascending</label>
                            </div>
                            <div class="form-check">
                                <input class="form-check-input" value="Descending" type="radio" id="descending" v-model="lowHigh" />
                                <label class="form-check-label" for="descending">Descending</label>
                            </div>
                        </div>
                    </div>
                    <div class="col-lg-10">
                        <form class="form-a">
                            <div class="row">
                                <div class="col-md-8 mb-2">
                                    <div class="form-group">
                                        <input type="text" v-model="searchInput" v-on:input="searchFilter" class="form-control p-2 ps-3 rounded-0 shadow-0 form-control-lg form-control-a" placeholder="Search Lesson">
                                    </div>
                                </div>
                                <div class="col-md-4 mb-2">
                                    <div class="form-group">
                                        <select name="sortBy" v-model="sortBy" v-on:change="filterLessons" class="form-control p-2 bg-white ps-3 form-select rounded-0 shadow-0 form-control-a form-control-lg">
                                            <option value="">--Sort By--</option>
                                            <option value="price">Price</option>
                                            <option value="location">Location</option>
                                            <option value="avalibility">Avalibility</option>
                                            <option value="subject">Subject</option>
                                        </select>
                                    </div>
                                </div>

                            </div>
                        </form>
                        <div class="product-list">
                            <div class="row">
                                <div v-for="lesson in lessons" class="col-lg-3 col-sm-6 col-md-6 mb-2" :key="lesson">
                                    <Lesson :lesson="lesson" v-on:add-to-cart="addToCart($event)" ></Lesson>
                                </div>
                             
                                <div v-if="lessons.length < 1">
                                    <div class="text-center mt-4">
                                        <img src="src/assets/images/empty-box.png" alt="" width="160" height="160">
                                        <p class="mt-2"><strong>{{searchInput}}</strong> not Find</p>
                                    </div>
                                </div>

                            </div>
                        </div>

                    </div>
                </div>

            </div>
            <div class="pt-5" v-else>
                <Checkout :cart="checkOutItems()" v-on:remove-lesson="removeCartItem($event)" v-on:checkout="checkOut($event)"></Checkout>
            </div>
        </div>

</template>




