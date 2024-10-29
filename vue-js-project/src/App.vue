<template>
  <div class="app-container">
    <header class="header">
      <h1>ShopEase <span>🛍️</span></h1>
      <p>Your one-stop shop for great products!</p>
    </header>
    
    <div class="product-list">
      <div
        v-for="product in products"
        :key="product.id"
        class="product-card"
      >
        <h2>{{ product.name }}</h2>
        <p class="price">₹{{ product.price }}</p>
        <input
          type="number"
          v-model.number="product.quantity"
          min="1"
          placeholder="Qty"
          class="quantity-input"
        />
        <button @click="addToCart(product)" class="add-to-cart-btn">Add to Cart</button>
      </div>
    </div>

    <div class="cart">
      <h2>Your Cart</h2>
      <ul v-if="cart.length">
        <li v-for="item in cart" :key="item.id" class="cart-item">
          {{ item.name }} - Qty: {{ item.quantity }} - ₹{{ item.price * item.quantity }}
        </li>
      </ul>
      <p v-else>Your cart is empty.</p>
      <div v-if="totalAmount > 0" class="checkout-section">
        <p>Total Amount: ₹{{ totalAmount }}</p>
        <button @click="generateQRCode" class="checkout-btn">Proceed to Payment</button>
        <div v-if="qrCodeUrl" class="qr-code">
          <img :src="qrCodeUrl" alt="QR Code for Payment" />
          <p>Scan to Pay with UPI ID: <strong>{{ upiId }}</strong></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: "Product 1", price: 20, quantity: 1 },
        { id: 2, name: "Product 2", price: 10, quantity: 1 },
        { id: 3, name: "Product 3", price: 30, quantity: 1 },
      ],
      cart: [],
      qrCodeUrl: "",
      upiId: "6299695907-3@ybl",
    };
  },
  computed: {
    totalAmount() {
      return this.cart.reduce((total, item) => total + item.price * item.quantity, 0);
    },
  },
  methods: {
    addToCart(product) {
      const cartItem = this.cart.find((item) => item.id === product.id);
      if (cartItem) {
        cartItem.quantity += product.quantity;
      } else {
        this.cart.push({ ...product });
      }
      product.quantity = 1;
    },
    async generateQRCode() {
      const upiLink = `upi://pay?pa=${this.upiId}&pn=ShopEase&am=${this.totalAmount}&cu=INR`;
      const response = await fetch(`https://api.qrserver.com/v1/create-qr-code/?data=${encodeURIComponent(upiLink)}&size=200x200`);
      this.qrCodeUrl = response.url;
    },
  },
};
</script>
