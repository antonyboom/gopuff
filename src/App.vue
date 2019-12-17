<template>
  <div id="app">
    <div class="cart">
      <ul class="items">
        <li class="item" :key="item.id" v-for="item in products">
          <div class="item-preview">
            <img :src="item.thumbnail" :alt="item.title" class="item-thumbnail">
            <div class="item-content">
              <h2 class="item-title">{{item.name}}</h2>
              <div class="item-description" v-html="item.description"></div>
            </div>
          </div>
          <div>
            <input type="text" class="item-quantity" v-model="item.quantity">
            <span class="item-price">${{item.price}}</span>
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
                item.discount = existedProduct.discount;
                item.thumbnail = item.avatar.small;
                return item;
              }
              return item
            });
          })
      }
    },
    created() {
      /* getting list of ids to load extended item info */
      this.getItemMetaData(this.response.cart.products.map(item => item.id))
    },
    computed: {
      getSubtotal() {
        return this.products
          .reduce((a, b) => a + b.price * b.quantity, 0)
          .toFixed(2)
      },
      getSubTotalDiscount() {
        return this.products
          .reduce((a, b) => a + b.discount * b.quantity, 0)
          .toFixed(2)
      },
      getTotal() {
        return (
          this.getSubtotal - this.getSubTotalDiscount
        )
      }
    },
    data() {
      return {
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
    color: #2c3e50;
    margin-top: 60px;
  }

  body {
    margin: 0;
    background: #f3f6f9;
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
    background: #ffffff;
    font-family: 'Helvetica Neue', Arial, sans-serif;
    font-size: 14px;
    color: #333a45;
    border-radius: 5px;
    padding: 16px;
  }

  .cart-footer {
    position: sticky;
    bottom: 0px;
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
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px 0;
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
    margin-right: 20px;
    max-width: 100px;
    max-height: 100px;
    border-radius: 3px;
  }

  .item-title {
    margin: 0 0 10px 0;
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
