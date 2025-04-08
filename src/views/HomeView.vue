<template>
  <div class="p-6 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <div class="col-span-2 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
      <div
        v-for="product in filteredProducts"
        :key="product.id"
        class="rounded-2xl shadow-md p-4 text-center"
      >
        <img :src="product.image" :alt="product.name" class="w-32 h-32 object-cover mb-4 mx-auto" />
        <h2 class="text-xl font-semibold mb-2">{{ product.name }}</h2>
        <p class="mb-2 text-gray-500">${{ product.price }}</p>
        <button @click="addToCart(product)" class="bg-blue-500 text-white px-4 py-2 rounded-xl hover:bg-blue-600">
          加入購物車
        </button>
      </div>
    </div>

    <div class="col-span-1">
      <div class="rounded-2xl shadow-lg p-4 sticky top-6">
        <h2 class="text-xl font-bold mb-4">🛒 購物車</h2>
        <div v-if="cart.length === 0" class="text-gray-500">購物車是空的</div>
        <ul v-else class="mb-4 space-y-1">
          <li v-for="(item, index) in cart" :key="index" class="flex justify-between">
            <span>{{ item.name }} - ${{ item.price }}</span>
            <button @click="removeFromCart(index)" class="text-red-500 hover:underline">移除</button>
          </li>
        </ul>
        <p class="font-semibold">總金額：${{ total }}</p>
        <button v-if="cart.length" @click="checkout" class="mt-4 bg-green-500 text-white px-4 py-2 rounded-xl hover:bg-green-600 w-full">
          結帳
        </button>
      </div>
    </div>

    <div class="col-span-3 mt-4">
      <input
        v-model="search"
        type="text"
        placeholder="搜尋商品..."
        class="w-full border border-gray-300 rounded-xl px-4 py-2"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted, watch } from 'vue';

interface Product {
  id: number;
  name: string;
  price: number;
  image: string;
}

export default defineComponent({
  setup() {
    const products = ref<Product[]>([
      { id: 1, name: 'T-Shirt', price: 20, image: 'https://via.placeholder.com/150' },
      { id: 2, name: 'Jeans', price: 40, image: 'https://via.placeholder.com/150' },
      { id: 3, name: 'Sneakers', price: 60, image: 'https://via.placeholder.com/150' },
    ]);

    const search = ref('');
    const filteredProducts = computed(() => {
      return products.value.filter(p =>
        p.name.toLowerCase().includes(search.value.toLowerCase())
      );
    });

    const cart = ref<Product[]>([]);

    const addToCart = (product: Product) => {
      cart.value.push(product);
    };

    const removeFromCart = (index: number) => {
      cart.value.splice(index, 1);
    };

    const total = computed(() => cart.value.reduce((sum, item) => sum + item.price, 0));

    const checkout = () => {
      alert(`感謝您的購買！總金額為 $${total.value}`);
      cart.value = [];
    };

    // LocalStorage 持久化
    const STORAGE_KEY = 'shopping-cart';

    onMounted(() => {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) {
        cart.value = JSON.parse(saved);
      }
    });

    watch(cart, (newCart) => {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(newCart));
    }, { deep: true });

    return {
      products,
      cart,
      addToCart,
      removeFromCart,
      total,
      search,
      filteredProducts,
      checkout,
    };
  },
});
</script>

<style scoped>
/* 可自定義樣式 */
</style>

