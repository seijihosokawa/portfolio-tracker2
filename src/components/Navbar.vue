<template>
  <nav>
    <div class="max-w-7xl mx-auto px-2 sm:px-6 lg:px-8">
      <div class="relative flex items-center justify-between h-16 space-x-4">
        <a
          href="https://finance.yahoo.com/"
          class="text-gray-300 hover:bg-gray-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium"
          >Yahoo Finance</a
        >
        <a
          href="https://finviz.com/map.ashx"
          class="text-gray-300 hover:bg-gray-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium"
          >Finviz Heat Map</a
        >
        <a
          href="https://www.fidelity.com/"
          class="text-gray-300 hover:bg-gray-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium"
          >Fidelity</a
        >
        <button
          @click="refresh"
          class="text-gray-300 hover:bg-gray-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium"
        >
          Refresh
        </button>

        <div class="image-upload hover:bg-gray-700 px-3 py-2 rounded-md">
          <label for="file-input">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-6 w-6 cursor-pointer"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12"
              />
            </svg>
          </label>
          <input
            id="file-input"
            type="file"
            accept=".csv"
            v-on:change="previewFiles"
          />
        </div>
      </div>
    </div>
  </nav>
</template>
<script>
export default {
  data() {
    return { data: String };
  },
  emits: ["updateCsvData"],
  methods: {
    passCsvToParent(csvString) {
      //passing a csv file converted into a string to parent component
      console.log("passCsvToParent", csvString);
      this.$emit("update-csv-data", csvString);
    },
    previewFiles(event) {
      var self = this;
      //console.log(event.target.files[0]);
      var reader = new FileReader();
      reader.onload = function () {
        this.data = reader.result;
        self.passCsvToParent(this.data);
      };
      reader.readAsBinaryString(event.target.files[0]);
    },
    refresh() {
      window.location.reload();
    },
  },
};
</script>