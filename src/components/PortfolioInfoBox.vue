<template>
  <div
    class="bg-black border-white-600 border-opacity-60 | p-1 border-solid rounded-2xl border-2 | cursor-pointer | hover:bg-red-600 hover:border-transparent | transition-colors duration-500"
    :class="{
      'hover:bg-green-400': positive,
      'border-green-400': positive,
      'hover:bg-red-600': !positive,
      'border-red-600': !positive,
    }"
  >
    <div class="grid grid-rows-2 grid-flow-col gap-2 content-center">
      <div class="flex self-top row-span-1 text-xs italic">{{ name }}</div>
      <div
        v-if="name === 'Overall Return' || name === 'Performance Today'"
        class="flex justify-center row-span-1 text-base"
      >
        <div v-if="value < 0">-</div>
        <div v-else>+</div>
        ${{ numberWithCommas(value) }}
      </div>
      <div
        v-else
        class="flex justify-center row-span-1 text-base"
        style="position: relative; top: -15px"
      >
        ${{ numberWithCommas(value) }}
      </div>
      <div
        class="flex justify-center self-center row-span-2 text-2xl items-end"
        v-if="name === 'Overall Return' || name === 'Performance Today'"
      >
        <div v-if="percentage < 0">-</div>
        <div v-else>+</div>
        {{ percentage }}%
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["name", "value", "percentage"],
  data() {
    return {
      positive: Boolean,
    };
  },
  methods: {
    getOverallReturn() {
      try {
        //console.log(this.value);
        this.positive = this.value >= 0 ? true : false;
      } catch (error) {
        console.log(error);
        return "error loading";
      }
    },
    numberWithCommas(x) {
      return String(x).replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  },
  mounted() {
    this.getOverallReturn();
  },
};
</script>