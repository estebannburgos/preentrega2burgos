<template>
    <div class="container">
        <div class="cart">
        <h2>Carrito de Compras</h2>
        <table class="table">
            <thead>
            <tr>
                <th>Producto</th>
                <th>Precio</th>
                <th class="quantity-column">Cantidad</th>
                <th>Subtotal</th>
                <th></th>
            </tr>
            </thead>
            <tbody>
                <tr v-for="item in cartItems" :key="item.id">
                <td>{{ item.product.name }}</td>
                <td>{{ formattedPrice(item.product.price) }}</td>
                <td class="quantity-column">
                    <button class="btn btn-sm btn-secondary" @click="decreaseQuantity(item)">-</button>
                    {{ item.quantity }}
                    <button class="btn btn-sm btn-secondary" @click="increaseQuantity(item)">+</button>
                </td>
                <td>{{ formattedPrice(item.product.price * item.quantity) }}</td>
                <td>
                    <button class="btn btn-sm btn-danger" @click="removeItem(item)">Eliminar</button>
                </td>
                </tr>
            </tbody>
            <tfoot>
            <tr>
                <td colspan="3">Total:</td>
                <td>{{ formattedPrice(totalPrice) }}</td>
                <td></td>
            </tr>
            </tfoot>
        </table>
    
        <div v-if="cartItems.length === 0" class="empty-cart">
            No hay productos en el carrito
        </div>
    
        <button v-else class="btn btn-primary" @click="confirmPurchase">Confirmar Compra</button>
        </div>
    </div>
  </template>
  
  <script>

  import { Cart } from '../classes/cart.js'

  export default {
    name: 'Cart',
    data() {
      return {
        // cartItems: [],
        cartDB : new Cart()  
      };
    },
    computed: {
        cartItems() {
            // return this.$store.state.cart;
            return this.cartInProgress;
        },
      totalPrice() {
        return this.$store.getters.cartTotalPrice;
      }
    },
    methods: {
        increaseQuantity(item) {
        this.$store.commit('increaseQuantity', item);
        },
        decreaseQuantity(item) {
        if (item.quantity > 1) {
            this.$store.commit('decreaseQuantity', item);
        }
        },
        removeItem(item) {
        this.$store.commit('removeItem', item);
        },
      formattedPrice(price) {
        return `CLP $${price}`;
      },
    //   confirmPurchase() {
        
    //     alert('¡Felicidades por tu compra!');
    //     this.cartItems = [];
    //   },
      async confirmPurchase() {
        let carrito = await this.cartDB.updateCart(this.userId, this.cartItems)
        this.$store.commit('clearCart');
        alert('¡Felicidades por tu compra!');
        setTimeout(() => {
          this.$router.push('/home')
        },3000)
      }
    }
  };
  </script>
  
  <style scoped>
  .container {
  display: block;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin-top: 10vh;
}
  .cart {
    max-width: 500px;
    margin: 0 auto;
    padding: 20px;
    background-color: #f5f5f5;
    border-radius: 8px;
  }
  
  h2 {
    text-align: center;
    color: #333;
  }
  
  table {
    width: 100%;
    margin-bottom: 20px;
    border-collapse: collapse;
  }
  
  th,
  td {
    padding: 10px;
    border-bottom: 1px solid #ccc;
  }
  
  tfoot td {
    font-weight: bold;
  }
  
  .btn-secondary {
    background-color: #6c757d;
    border-color: #6c757d;
    color: #fff;
    margin-left: 5px;
  }
  
  .btn-danger {
    background-color: #dc3545;
    border-color: #dc3545;
    color: #fff;
  }
  
  .btn-primary {
    background-color: #007bff;
    border-color: #007bff;
    color: #fff;
  }
  
  .empty-cart {
    text-align: center;
    color: #555;
  }
  .quantity-column {
  width: 180px;
}
  </style>
  