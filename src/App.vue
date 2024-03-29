<template>
  <div id="app">
    <div class="search">
      <label for="search" class="material-input">
        <input type="text" id="search" placeholder="" v-model="search">
        <span class="label">Find item in cart</span>
        <span class="border"></span>
      </label>
    </div>
    <div class="cart">
      <ul class="items">
        <li class="item" :key="item.id" v-for="item in filteredList">
          <div class="item-preview">
            <img :src="item.thumbnail" :alt="item.title" class="item-thumbnail">
            <div class="item-content">
              <h2 class="item-title">{{item.name}}</h2>
              <div class="item-description" v-html="item.description"></div>
            </div>
          </div>
          <div>
            <button class="clear-item" v-on:click="deleteProduct(item)">X</button>
            <input type="text" class="item-quantity" v-model="item.quantity">
            <span class="item-price">${{item.price}}</span>
            <span v-if="!!item.credit_coupon_price" class="credit-item">
              Your item cost: $ {{(item.price - item.credit_coupon_price >= 0) ? (item.price - item.credit_coupon_price).toFixed(2) : 0}}
            </span>
          </div>
        </li>
      </ul>
      <div class="cart-footer">
        <h3 class="cart-line">
          Subtotal <span class="cart-price">$ {{getSubtotal}}</span>
        </h3>
        <h3 class="cart-line">
          Discount <span class="cart-price">$ {{getSubTotalDiscount}}</span>
        </h3>
        <h3 class="cart-line">
          Total <span class="cart-total">$ {{getTotal}}</span>
        </h3>
      </div>
    </div>
  </div>
</template>

<script>
  import json from './assets/order.json'

  export default {
    name: 'app',
    components: {},
    methods: {
      getItemMetaData(ids) {
        fetch(`https://prodcat.gopuff.com/api/products?location_id=-1&product_ids=${ids}`)
          .then(res => res.json())
          .then(json => {
            /* list of products */
            this.products = json.products.map(item => {
              const existedProduct = this.response.cart.products.find(p => p.product_id === item.product_id);
              if (existedProduct) {
                item.quantity = existedProduct.quantity;
                item.credit_coupon_price = existedProduct.credit_coupon_price;
                item.thumbnail = item.avatar.small;
                return item;
              }
              return item
            });
          })
      },
      deleteProduct(item) {
        this.products = this.products.filter(p => p !== item)
      }
    },
    created() {
      /* getting list of ids to load extended item info */
      this.getItemMetaData(this.response.cart.products.map(item => item.id))
    },
    computed: {
      filteredList() {
        return this.products.filter(p => {
          return p.name.toLowerCase().includes(this.search.toLowerCase())
        })
      },
      getSubtotal() {
        return this.products
          .reduce((a, b) => a + b.price * b.quantity, 0)
          .toFixed(2)
      },
      getSubTotalDiscount() {
        return this.products
          .reduce((a, b) => a + b.credit_coupon_price * b.quantity, 0)
          .toFixed(2)
      },
      getTotal() {
        return (
          (this.getSubtotal - this.getSubTotalDiscount).toFixed(2)
        )
      }
    },
    data() {
      return {
        search: '',
        response: json,
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
  }

  body {
    margin: 0;
    background: #f3f6f9;
    padding: 24px;
  }

  .items {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  .cart {
    background: #ffffff;
    font-family: 'Helvetica Neue', Arial, sans-serif;
    font-size: 14px;
    color: #333a45;
    border-radius: 8px;
    padding: 16px;
  }

  .cart-footer {
    position: sticky;
    bottom: 12px;
    z-index: 1;
    background: #3bb1e6;
    padding: 4px 16px;
    border-radius: 16px;
    display: flex;
    flex-direction: column;
  }

  .cart-line {
    display: flex;
    justify-content: space-between;
    margin: 4px;
    align-items: center;
    font-weight: bold;
    color: #fff;
    border-bottom: 1px solid #ffffff;
  }

  .cart-price {
    font-size: 15px;
  }

  .cart-total {
    font-size: 16px;
  }

  .item {
    position: relative;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 0;
    border-bottom: 2px solid rgba(51, 58, 69, 0.1);
  }

  .item-preview {
    display: flex;
    align-items: start;
    width: 80%;
  }

  .item-content {
    display: flex;
    flex-direction: column;
    align-items: start;
  }

  .item-thumbnail {
    margin-right: 24px;
    max-width: 100px;
    max-height: 100px;
    border-radius: 3px;
  }

  .item-title {
    margin: 0 0 12px 0;
    font-size: inherit;
  }

  .item-description {
    margin: 0;
    color: rgba(51, 58, 69, 0.6);
    display: flex;
    flex-direction: column;
    align-items: start;
  }

  .item-description p {
    text-align: left !important;
    margin: 0;
  }

  li {
    text-align: left !important;
    margin: 0;
  }

  .item-quantity {
    max-width: 24px;
    padding: 8px 12px;
    font-size: inherit;
    color: rgba(51, 58, 69, 0.8);
    border: 2px solid rgba(51, 58, 69, 0.1);
    border-radius: 3px;
    text-align: center;
  }

  .item-price {
    margin-left: 24px;
  }

  /* credit item styles */
  .credit-item {
    background: #e63b62;
    border-radius: 16px;
    position: absolute;
    width: 150px;
    right: 0;
    bottom: 10px;
    padding: 4px;
    color: white;
    text-align: center;
  }

  .credit-item:hover {
    animation: shake 0.82s cubic-bezier(.36,.07,.19,.97) both;
    transform: translate3d(0, 0, 0);
    backface-visibility: hidden;
    perspective: 1000px;
  }

  @keyframes shake {
    10%, 90% {
      transform: translate3d(-1px, 0, 0);
    }

    20%, 80% {
      transform: translate3d(2px, 0, 0);
    }

    30%, 50%, 70% {
      transform: translate3d(-4px, 0, 0);
    }

    40%, 60% {
      transform: translate3d(4px, 0, 0);
    }
  }

  /* search styles */
  .search {
    position: sticky;
    top: 0;
    background: #f3f6f9;
    z-index: 100;
  }

  .material-input {
    position: relative;
    margin: auto;
    width: 100%;
    max-width: 280px;
  }

  .material-input .label {
    position: absolute;
    top: 16px;
    left: 0;
    font-size: 16px;
    color: #9098a9;
    font-weight: 500;
    transform-origin: 0 0;
    transition: all 0.2s ease;
  }

  .material-input .border {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 2px;
    width: 100%;
    background: #3bb1e6;
    transform: scaleX(0);
    transform-origin: 0 0;
    transition: all 0.15s ease;
  }

  .material-input input {
    -webkit-appearance: none;
    width: 100%;
    border: 0;
    font-family: inherit;
    padding: 12px 0;
    height: 48px;
    font-size: 16px;
    font-weight: 500;
    border-bottom: 2px solid #c8ccd4;
    background: none;
    border-radius: 0;
    color: #223254;
    transition: all 0.15s ease;
  }

  .material-input input:hover {
    background: rgba(34, 50, 84, 0.03);
  }

  .material-input input:not(:placeholder-shown) + span {
    color: #5a667f;
    transform: translateY(-26px) scale(0.75);
  }

  .material-input input:focus {
    background: none;
    outline: none;
  }

  .material-input input:focus + span {
    color: #3bb1e6;
    transform: translateY(-26px) scale(0.75);
  }

  .material-input input:focus + span + .border {
    transform: scaleX(1);
  }

  /* clear button */

  .clear-item {
    background-color: #e63b62;
    border: none;
    color: white;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 2px;
    border-radius: 50%;
    width: 24px;
    height: 24px;
    position: absolute;
    top: 0;
    right: 0;
  }

  .clear-item:hover {
    transform: scale(1.1);
  }
</style>
