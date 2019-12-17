<template>
  <div id="app">
    <div class="cart">
      <h1 class="title">Order</h1>
      <ul class="items">
        <li class="item" :key="item.id" v-for="item in products">
          <div class="item-preview">
            <img src="" alt="" class="item-thumbnail">
            <div>
              <h2 class="item-title">{{item.name}}</h2>
              <p class="item-description">{{item.description}}</p>
            </div>
          </div>
          <div>
            <input type="text" class="item-quantity">
            <span class="item-price">${{item.price}}</span>
          </div>
        </li>
      </ul>
      <h3 class="cart-line">
        Subtotal <span class="cart-price">${{subTotal}}</span>
      </h3>
      <h3 class="cart-line">
        Discount <span class="cart-price">${{discount}}</span>
      </h3>
      <h3 class="cart-line">
        Total <span class="cart-price cart-total">${{finalPrice}}</span>
      </h3>
    </div>
  </div>
</template>

<script>
  import json from './assets/order.json'

  export default {
    name: 'app',
    components: {},
    methods: {
      getSubTotal(items) {
        return items
          .map(item => {
              return item.price * item.quantity
            })
          .reduce((a, b) => a + b, 0)
          .toFixed(2);
      },
      getSubTotalDiscount() {
        return this.response.cart.products
          .map(item => {
              return item.discount * item.quantity
            })
          .reduce((a, b) => a + b, 0)
          .toFixed(2);
      },
      getItemMetaData(ids) {
        fetch(`https://prodcat.gopuff.com/api/products?location_id=-1&product_ids=${ids}`)
        .then(res => res.json())
        .then(json => {
          /* list of products */
          this.products = json.products;
          /* update variable to display total cost of the cart  */
          this.subTotal = this.getSubTotal(this.products);
          this.discount = this.getSubTotalDiscount(this.products);
          this.finalPrice =  this.subTotal - this.discount;
        })
      }
    },
    created() {
      /* getting list of ids to load extended item info */
      this.getItemMetaData(this.cart.map(item => item.id))
    },
    data() {
      return {
        cart: json.cart.products,
        products: [],
        subTotal: 0,
        discount: 0,
        finalPrice: 0
      }
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  body {
    margin: 0;
    background: #fdca40;
    padding: 30px;
  }

  .title {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 0;
    text-transform: uppercase;
    font-size: 110%;
    font-weight: normal;
  }

  .items {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  .cart {
    background: #fff;
    font-family: 'Helvetica Neue', Arial, sans-serif;
    font-size: 16px;
    color: #333a45;
    border-radius: 3px;
    padding: 30px;
  }

  .cart-line {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 20px 0 0 0;
    font-size: inherit;
    font-weight: normal;
    color: rgba(51, 58, 69, 0.8);
  }

  .cart-price {
    color: #333a45;
  }

  .cart-total {
    font-size: 130%;
  }

  .item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px 0;
    border-bottom: 2px solid rgba(51, 58, 69, 0.1);
  }

  .item-preview {
    display: flex;
    align-items: center;
  }

  .item-thumbnail {
    margin-right: 20px;
    border-radius: 3px;
  }

  .item-title {
    margin: 0 0 10px 0;
    font-size: inherit;
    display: flex;
  }

  .item-description {
    margin: 0;
    color: rgba(51, 58, 69, 0.6);
  }

  .item-quantity {
    max-width: 30px;
    padding: 8px 12px;
    font-size: inherit;
    color: rgba(51, 58, 69, 0.8);
    border: 2px solid rgba(51, 58, 69, 0.1);
    border-radius: 3px;
    text-align: center;
  }

  .item-price {
    margin-left: 20px;
  }
</style>
