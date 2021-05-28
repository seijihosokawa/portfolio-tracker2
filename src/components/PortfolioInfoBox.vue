<template>
  <div
    class="bg-black border-white-600 border-opacity-60 | p-1 border-solid rounded-2xl border-2 | cursor-pointer | hover:bg-red-600 hover:border-transparent | transition-colors duration-500"
    :class="{
      'hover:bg-green-400': value > 0,
      'border-green-400': value > 0,
      'hover:bg-red-600': value <= 0,
      'border-red-600': value <= 0,
    }"
  >
    <div class="grid grid-rows-2 grid-flow-col gap-2 content-center">
      <div class="flex self-top row-span-1 text-xs italic">{{ name }}</div>
      <div
        v-if="name === 'Overall Return' || name === 'Performance Today'"
        class="flex justify-center row-span-1 text-base"
      >
        <div v-if="value > 0">+</div>
        {{ numberWithCommas(value) }}
      </div>
      <div
        v-else
        class="flex justify-center row-span-1 text-base"
        style="position: relative; top: -15px"
      >
        {{ numberWithCommas(value) }}
      </div>
      <div
        class="flex justify-center self-center row-span-2 text-2xl items-end"
        v-if="name === 'Overall Return' || name === 'Performance Today'"
      >
        <div v-if="percentage > 0">+</div>
        {{ percentage }}%
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["name", "value", "percentage"],
  data() {
    return {};
  },
  methods: {
    numberWithCommas(x) {
      return String(x).replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  },
};
</script>