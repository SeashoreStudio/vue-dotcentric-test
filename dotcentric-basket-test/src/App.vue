<script setup>
  import { ref, computed, onMounted } from "vue";
  import Product from "@/components/Product.vue";

  const products = ref([]);
  const cartData = ref([]);
  const cartTotals = ref([]);

  onMounted(async () => {
    products.value = await getProducts();
    cartData.value = await getCartData();
    cartTotals.value = await getCartTotals();
  });

  const getCartTotal = computed(() =>
    products.value.reduce(
      (total, product) => total + product.unitPrice * product.quantity,
      0
    )
  );

  const getCartCurrency = computed(() => {
    return cartData.value.currency;
  })

  const getCartShipping = computed(() => {
    return cartTotals.value.delivery == 'FREE' ? 0.00 : cartTotals.value.delivery;
  })

  const getCartTax = computed(() => {
    return getCartTotal.value / 6;
  })

  const getCartSubTotal = computed(() => {
    return getCartTotal.value - getCartTax.value;
  })
  
  const getCartData = async () => {
    const results = await fetch("http://localhost:3000/details");
    const cartData = await results.json();
    return cartData;
  };

  const getCartTotals = async () => {
    const results = await fetch("http://localhost:3000/totals");
    const cartTotals = await results.json();
    return cartTotals;
  };

  /* Get all products from backend with json server */
  const getProducts = async () => {
    const results = await fetch("http://localhost:3000/items");
    const productsData = await results.json();
    return productsData;
  };

  /* Get product by SKU as no product ID exists */
  const getProduct = async (productCode) => {
    const result = await fetch(`http://localhost:3000/items?code=${productCode}`);
    const productData = await result.json();
    return productData;
  };

  const removeProductFromCart = async ($event) => {
    const productCode = $event;
    const response = await fetch(`http://localhost:3000/items?code=${productCode}`, {
      method: "DELETE"
    });
    products.value = products.value.filter((product) => product.code !== productCode);
  };

  const increaseQuantity = async ($event) => {
    const productCode = $event;
    const productPosition = products.value.findIndex(
      (product) => product.code == productCode
    )

    if(products.value[productPosition].maximumQuantity > products.value[productPosition].quantity) {
      products.value[productPosition].quantity = products.value[productPosition].quantity +=1;
      products.value[productPosition].value = products.value[productPosition].unitPrice * products.value[productPosition].quantity;
    }
  };

  const decreaseQuantity = async ($event) => {
    const productCode = $event;
    const productPosition = products.value.findIndex(
      (product) => product.code === productCode
    );

    // if quantity is 1, remove item
    if (products.value[productPosition].quantity === 1) removeProductFromCart(productCode);
    else {
      products.value[productPosition].quantity = products.value[productPosition].quantity -=1;
      products.value[productPosition].value = products.value[productPosition].unitPrice * products.value[productPosition].quantity;
    }
  };

</script>

<template>
  <div class="cart">
    <Product
      v-for="product in products"
      :key="product.code"
      :product="product"
      @remove-product="removeProductFromCart"
      @decrease-quantity="decreaseQuantity"
      @increase-quantity="increaseQuantity"
    />
    <div class="cart__totals">
      <div class="total">
        <p>Subtotal</p>
        <p>{{ getCartCurrency }}{{ getCartSubTotal.toFixed(2) }}</p>
      </div>
      <div class="total">
        <p>Shipping</p>
        <p>{{ getCartCurrency }} {{ getCartShipping }}</p>
      </div>
      <div class="total">
        <p>Tax</p>
        <p>{{ getCartCurrency }}{{ getCartTax.toFixed(2) }}</p>
      </div>
      <div class="total total--final">
        <p>Order Total</p>
        <p>{{ getCartCurrency }}{{ getCartTotal.toFixed(2) }}</p>
      </div>
      <button href="#" class="checkout__button">Checkout</button>
    </div>
  </div>
</template>

<style scoped>

</style>
