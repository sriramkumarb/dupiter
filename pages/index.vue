<template class=" container-full bg-gray-800 w-screen h-screen" >
  <div>
    <div class="mx-10 my-10">
      <h1 class="text-center font-mono text-red-800 text-4xl">
        <b>
          <i>Dupiter</i>
        </b>
      </h1>
    </div>
    <br />
    <div
      class="bg-gray-700 text-red-500 lg:mx-64 md:mx-64 p-4 rounded border-transparent mx-10 my-20 shadow-2xl"
    >
      <div class="my-10 mx-5 flex justify-center items-center">
        <input
          type="file"
          class="border border-black rounded w-56 bg-white"
          @change="chooseFile($event)"
        />
      </div>
      <div class="my-10 mx-5 flex justify-center items-center text-center">
        <select v-model="headerToClean">
          <option v-for="(h, index) in headers" :key="index" :value="h" class="bg-gray-300">{{ h }}</option>
        </select>
      </div>
      <div class="my-10 mx-5 flex justify-center items-center">
        <button @click="downloadCSV()">Download</button>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import Vue from "vue";

let file: any = "";
let objectStore: any = {};
let cleanData: any[] = [];

export default Vue.extend({
  data() {
    return {
      fileContent: "",
      headerToClean: "",
      data: [],
      headers: [] as any,
      csvOutput: "",
    };
  },
  mounted() {},
  methods: {
    chooseFile(event: any) {
      file = event.target.files[0];

      if (!file) {
        alert("Please choose a file");
        return true;
      }

      // Read the file content
      const reader: FileReader = new FileReader();
      reader.readAsText(file);

      reader.onload = () => {
        this.fileContent = reader.result as string;
        this.parseCSV(this.fileContent);
      };
    },
    // Parsing the CSV Headers and Data
    parseCSV(fileContent: string) {
      const lineArray = fileContent.split("\n");
      this.headers = lineArray[0].split(",") as [];
      const data: any = [];

      lineArray.splice(1).forEach((line) => {
        data.push(line.split(","));
      });
      this.data = data;
    },
    // Removing the duplicates in CSV File
    removeDuplicates() {
      const index = this.headers.indexOf(this.headerToClean);
      cleanData = [];
      objectStore = {};
      this.data.forEach((row) => {
        if (!objectStore[row[index]]) {
          objectStore[row[index]] = row;
          cleanData.push(row);
        }
      });
    },
    // Rebuilding the CSV File from Text Data
    buildCSVFile() {
      this.csvOutput = "";
      this.csvOutput = this.headers.join(",") + "\n";
      cleanData.forEach((row: any[]) => {
        this.csvOutput += row.join(",") + "\n";
      });
    },
    // Process and Download as CSV File
    downloadCSV() {
      this.removeDuplicates();
      this.buildCSVFile();
      const element = document.createElement("a");
      element.setAttribute(
        "href",
        "data:text/csv;charset=utf-8," + encodeURIComponent(this.csvOutput)
      );
      element.setAttribute("download", "output.csv");
      element.style.display = "none";
      document.body.appendChild(element);
      element.click();
      document.body.removeChild(element);
    },
  },
});
</script>

<style>
</style>

