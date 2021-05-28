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
      <div class="flex justify-center row-span-1 text-base">${{ value }}</div>
      <div
        class="flex justify-center self-center row-span-2 text-2xl items-end"
      >
        +40.4%
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["name", "value"],
  data() {
    return {
      overallReturn: Number,
      dayChange: Number,
      dayPercentChange: Number,
      positive: Boolean,
    };
  },
  methods: {
    getOverallReturn() {
      try {
        this.overallReturn = 5000;
        this.dayChange = 200;
        this.dayPercentChange = 3;
        this.positive = this.dayChange >= 0 ? true : false;
      } catch (error) {
        console.log(error);
        return "error loading";
      }
    },
  },
  created() {
    this.getOverallReturn();
  },
};
</script>