<script setup>
defineProps(["product"]);
defineEmits(["remove-product", "decrease-quantity", "increase-quantity"]);
</script>

<template>
  <div class="product">
    <div class="product__imagecontainer">
      <a :href="product.productLink"><img :src="product.productImage.defaultLoadImage.thumbnail" :alt="product.productImage.defaultLoadImage.altText" width="100" class="product__image" /></a>
    </div>
    <div class="product__info">
      <a :href="product.productLink">{{ product.displayName }}</a><br>
      £{{ product.value }}<br>
      <small>{{ product.messages }}</small>

      <div class="product__actions">
        <div class="product__quantity">
          <button class="decrement-btn" @click="$emit('decrease-quantity', product.code)"> - </button>
          <div class="product__quantityamount">{{ product.quantity }}</div>
          <button class="increment-btn" @click="$emit('increase-quantity', product.code)"> + </button>
        </div>
        <button class="remove-btn" @click="$emit('remove-product', product.code)">
           Remove
        </button>
      </div>
    </div>
  </div>
</template>