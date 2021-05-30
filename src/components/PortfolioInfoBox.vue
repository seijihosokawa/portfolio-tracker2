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
    <div
      v-if="parseInt(value) == 0"
      class="grid grid-rows-2 grid-flow-col gap-2 content-center"
    >
      <div class="flex self-top row-span-1 text-xs italic">
        {{ name }}
      </div>
      <div class="flex justify-center row-span-4">
        <div wire:loading class="relative mr-2 mt-2">
          <center>
            <svg
              class="animate-spin h-4 w-4 rounded-full bg-transparent border-2 border-transparent border-opacity-90"
              style="border-right-color: white; border-top-color: white"
              viewBox="0 0 24 24"
            ></svg>
          </center>
          Upload CSV
        </div>
      </div>
    </div>
    <div v-else class="grid grid-rows-2 grid-flow-col gap-2 content-center">
      <div class="flex self-top row-span-1 text-xs italic">
        {{ name }}
      </div>
      <div
        v-if="name === 'Overall Return' || name === 'Performance Today'"
        class="flex justify-center row-span-1 text-base"
      >
        <div v-if="percentage > 0">+</div>
        {{ percentage }}%
      </div>
      <div
        class="flex justify-center self-center row-span-2 text-2xl items-end"
        v-if="name === 'Overall Return' || name === 'Performance Today'"
      >
        <div v-if="value > 0">+</div>
        {{ numberWithCommas(value) }}
      </div>
      <div
        v-else
        class="flex justify-center self-center row-span-4 text-2xl items-center"
      >
        ${{ numberWithCommas(value) }}
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