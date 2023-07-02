<template>
  <div class="d-flex flex-column justify-content-start align-items-center" >
    <div class="w-100 px-3" >
      <div class="w-100 d-flex justify-content-start mb-5">
        <input type="file" accept=".csv" @change="handleFileUpload">
      </div>
      <table class="w-100" >
        <thead>
          <tr class="border">
            <th v-for="(header,index) in headers" :key="index" class="py-2 text-nowrap" :class="{'d-flex justify-content-start':index===0}">{{ header }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, index) in rows" :key="index" class="border" :class="{'bg-dark bg-gradient text-light':index%2===0}" style="--bs-bg-opacity: .5;">
            <td v-for="(cell, cellIndex) in row" :key="cellIndex" class="py-2 text-nowrap" :class="{'d-flex justify-content-start':cellIndex===0}">{{ cell }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style>
</style>
<script>
export default {
  data() {
    return {
      headers: [],
      rows: [],
    };
  },
  methods: {
    handleFileUpload(event) {
      this.headers = [];
      this.rows = [];
      const file = event.target.files[0];
      const reader = new FileReader();

      reader.onload = (event) => {
        const csvData = event.target.result;
        this.parseCSVData(csvData);
      };

      reader.readAsText(file);
    },
    parseCSVData(csvData) {
      const lines = csvData.split('\n');
      this.headers = lines[0].split(',');

      for (let i = 1; i < lines.length-1; i++) {
        const cells = lines[i].split(',');
        
        // Проверяем, имеют ли строки одинаковое количество значений
        if (cells.length !== this.headers.length) {
          // Добавляем пустые значения для создания соответствующего количества ячеек
          while (cells.length < this.headers.length) {
            cells.push('');
          }
        }
        
        this.rows.push(cells);
      }
    },
  },
};
</script>
