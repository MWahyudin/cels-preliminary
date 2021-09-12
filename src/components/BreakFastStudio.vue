<template>
  <div class="leading-normal text-gray-900 bg-gray-100">
    <div class="max-w-5xl p-4 mx-auto">
      <img class="max-w-full mx-auto my-8 sm:max-w-sm"
        src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/45561/undraw_Ordinary_day_3gk3.svg"
        alt="A drawing of two houses in a neighborhood"
      />
      <h1 class="my-4 text-2xl font-black text-center md:text-4xl lg:text-5xl">
        <span class="concert-underline">Concert Breakfast Studio</span>
      </h1>
      <nav
        class="flex flex-col p-2 mb-8 overflow-hidden text-sm bg-gray-300 rounded shadow md:flex-row"
      >
        <label class="sr-only" for="search">Search</label>
        <input
        v-model="keyword"
          class="flex-grow p-2 mb-2 rounded md:mb-0"
          type="search"
          placeholder="Search for foods"
          id="search"
        />
        <select class="mb-2 md:ml-2 md:mb-0" v-model="selectedGroup" name="group_select" id="group_select">
 <option value="" selected>group</option>
<template v-for="(group , i) in listGroup" :key="i">

          <option :value="group"> {{ group.toUpperCase() }} </option>
  </template>

        </select>
        <button
          class="p-2 rounded md:ml-2"
          style="background-color: var(--concert-blue)"
        >
          Place Order ( {{cart.length}} )
        </button>
      </nav>
      <div class="grid mb-8">
        <div v-for="(item , i) in (keyword !== '') ? listFilteredName  : selectedGroup !== '' ? listFilteredGroup : listMenu" :key="i">
        <!-- START CARD -->
        <div
          class="relative max-w-sm py-4 overflow-hidden text-center bg-white rounded shadow-lg "
        >
          <!-- ONLY SHOW IF NOT YET ADDED TO CARD -->
          <button
            class="absolute top-0 right-0 mt-2 mr-2 text-gray-600 hover:text-green-600" @click="addItem(item)"
          >
            <svg
              class="fill-current"
              xmlns="http://www.w3.org/2000/svg"
              xmlns:xlink="http://www.w3.org/1999/xlink"
              aria-hidden="true"
              focusable="false"
              width="2em"
              height="2em"
              style="
                -ms-transform: rotate(360deg);
                -webkit-transform: rotate(360deg);
                transform: rotate(360deg);
              "
              preserveAspectRatio="xMidYMid meet"
              viewBox="0 0 1024 1024"
            >
              <path
                d="M696 480H544V328c0-4.4-3.6-8-8-8h-48c-4.4
                0-8 3.6-8 8v152H328c-4.4 0-8 3.6-8 8v48c0 4.4
                3.6 8 8 8h152v152c0 4.4 3.6 8 8 8h48c4.4
                 0 8-3.6 8-8V544h152c4.4 0 8-3.6 8-8v-48c0-4.4-3.6-8-8-8z"
              ></path>
              <path
                d="M512 64C264.6 64 64 264.6 64 512s200.6
                 448 448 448 448-200.6 448-448S759.4 64 512
                  64zm0 820c-205.4 0-372-166.6-372-372s166.6-372
                   372-372 372 166.6 372 372-166.6 372-372 372z"
              ></path>
            </svg>
          </button>
          <!-- ONLY SHOW IF ADDED TO THE CART -->
          <button class="absolute top-0 left-0 mt-2 mr-2 text-gray-600 hover:text-red-600"
          v-if="cart.some(cart => cart.name === item.name && cart.quantity >= 1)" @click="removeItem(item)">
            <svg
              class="fill-current"
              viewBox="0 0 20 20"
              width="1.9em"
              height="1.9em"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M10 20c5.523 0 10-4.477 10-10S15.523
                 0 10 0 0 4.477 0 10s4.477 10 10 10zm0-2a8 8 0 100-16 8 8 0 000 16zm5-9v2H5V9h10z"
                fill-rule="evenodd"
              ></path>
            </svg>
          </button>
         <!-- ADDED TO THE CART -->

          <img class="mx-auto" :src="item.image" :alt="item.name" />
          <div class="px-6 pt-4">
            <div class="mb-2 text-xl font-bold"> {{ item.name }} ({{ itemQuantity(item.name) ?? 0}})</div>
          </div>
          <div class="px-6 py-4">
           <div v-for="(group , i) in item.groups" :key="i">
              <span
              class="inline-block px-3 py-1 mb-2 mr-2 text-sm font-semibold text-gray-700 bg-gray-200 rounded-full "
              >{{ group }}</span
            >
           </div>
          </div>
        </div>
        <!-- END CARD -->
</div>
      </div>

    </div>
  </div>
</template>

<script>
import {
  reactive, toRefs, computed, watch,
} from 'vue';
import MENU from '@/const/kitchen';

export default {
  setup() {
    const state = reactive({
      listMenu: MENU,
      listFilteredGroup: [],
      listFilteredName: [],
      selectedGroup: '',
      cart: [],
      keyword: '',
    });

    const listGroup = computed(() => {
      const groups = state.listMenu.map((a) => a.groups);
      const group = [...new Set(groups.flat(1))];
      return group;
    });

    const itemQuantity = (itemName) => {
      const itemIndex = state.cart.findIndex((cart) => cart.name === itemName);
      return state.cart[itemIndex]?.quantity;
    };

    const addItem = (item) => {
      const itemExist = state.cart.filter((cart) => cart.name === item.name);
      const itemName = itemExist[0]?.name;
      const itemIndex = state.cart.findIndex((cart) => cart.name === itemName);

      if (!itemExist.length) {
        state.cart.push({ ...item, quantity: 1 });
      } else {
        state.cart.push('');
        state.cart[itemIndex].quantity += 1;
        const arr = state.cart.filter((e) => e);
        state.cart = arr;
      }
    };
    const removeItem = (item) => {
      const itemIndex = state.cart.findIndex((cart) => cart.name === item.name);
      if (state.cart[itemIndex].quantity > 0) {
        state.cart[itemIndex].quantity -= 1;
      } else {
        state.cart.splice(itemIndex, 1);
      }
    };
    const itemFilterName = () => {
      const items = state.listMenu.filter((item) => item.name.toLowerCase().indexOf(state.keyword.toLowerCase()) > -1);
      state.listFilteredName = items;
    };

    const itemFilterGroup = () => {
      const items = state.listMenu.filter((e) => e.groups.some((group) => group === state.selectedGroup));
      state.listFilteredGroup = items;
    };

    watch(state, (currState) => {
      if (currState.selectedGroup !== '') {
        itemFilterGroup();
      }

      // filter by name
      if (currState.keyword !== '') {
        itemFilterName();
      }
    });

    return {
      ...toRefs(state),
      listGroup,
      addItem,
      removeItem,
      itemQuantity,
      itemFilterName,
      itemFilterGroup,
    };
  },
};
</script>

<style>
:root {
  --concert-blue: #5fdce3;
}

.grid {
  display: grid;
  grid-gap: 1em;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
}

.concert-underline {
  display: inline-block;
  position: relative;
  z-index: 0;
}

.concert-underline::after {
  display: inline-block;
  content: "";
  height: 1em;
  width: 100%;
  background: var(--concert-blue);
  left: 0;
  bottom: 0.25em;
  position: absolute;
  transform: skew(45deg) scale(0.9);
  z-index: -1;
  opacity: 0.5;
}

.concert-underline::before {
  display: inline-block;
  content: "";
  height: 1em;
  width: 100%;
  background: yellow;
  top: 0;
  left: -0.25em;
  position: absolute;
  transform: skew(45deg) scale(0.9) translateX(-0.5em);
  z-index: -1;
  opacity: 0.5;
}
</style>
