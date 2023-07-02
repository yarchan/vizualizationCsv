<template>
  <div class="d-flex flex-column justify-content-start align-items-center" >
    <div class="w-100 px-3" >
      <div class="w-100 d-flex justify-content-start mb-5">
        <div class="file-upload me-5">
          <input id="fileInputMember" type="file" accept=".csv" @change="getMembers">
          <label for="fileInputMember">Выберите файл с участниками</label> 
        </div>
        <div class="file-upload me-5">
          <input id="fileInputLogs" type="file" accept=".csv" @change="getLogs">
          <label for="fileInputLogs">Выберите файл с логами</label>
        </div>
        <div v-if="id_logs" @click="id_logs_show = !id_logs_show" class="d-flex flex-column" >
          <btn class="btn text-dark" style="background-color: #eaeaea;">
            <span class="me-3">
              Выберите id лога
            </span>
            <img src="../assets/ArrowBlack-172616f5.svg" alt="" style="transform: rotate(180deg);">
          </btn>
          <div v-if="id_logs_show" class="" style="max-height: 200px; overflow-y: auto;">
            <div class="" v-for="id_log in id_logs" :key="id_log"  @click="handleClick(id_log)" style="cursor:pointer">
              {{ id_log }}
            </div>
          </div>
        </div>
      </div>
      <div class="d-flex " style="text-align:start;">
        {{ log }}
      </div>
      <table class="w-100" >
        <thead>
          <tr class="border">
            <th v-for="(header,index) in headers" :key="index" class="py-2 text-nowrap" :class="{'d-flex justify-content-start':index===0}">{{ header }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, index) in rows" :key="index" class="border" :class="{'bg-dark bg-gradient text-light':index%2===0}" style="--bs-bg-opacity: .5;">
            <td v-for="(cell, cellIndex) in row" :key="cellIndex"  class="py-2 text-nowrap" :class="{'d-flex justify-content-start':cellIndex===0}">{{ cell }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style>
.file-upload {
  position: relative;
  display: inline-block;
}

.file-upload input[type="file"] {
  position: absolute;
  left: -9999px;
}

.file-upload label {
  display: inline-block;
  padding: 8px 12px;
  background-color: #eaeaea;
  border-radius: 4px;
  cursor: pointer;
}

.file-upload label:hover {
  background-color: #d4d4d4;
}
</style>
<script>
import {ref} from 'vue'

export default {
  data() {
    return {
      headers: [],
      rows: [],
      lines :[],
      log:ref(''),
      id_logs:ref(''),
      id_logs_show:ref(false)
    };
  },
  methods: {
    handleClick(id_log) {
      console.log(id_log);
      this.log = this.lines.filter(item => this.extractId(item) === id_log)
      this.log = this.log.join(", ");
    },
    extractId(id_log) {
      const startIndex = id_log.indexOf('id=') + 3;
      const endIndex = id_log.indexOf(',', startIndex);
      const extractedId = id_log.substring(startIndex, endIndex);
      return extractedId;
    },
    getLogs(event) {
      this.headers = [];
      this.rows = [];
      const file = event.target.files[0];
      const reader = new FileReader();

      reader.onload = (event) => {
        const csvData = event.target.result;
        this.parseLogs(csvData);
      };

      reader.readAsText(file);
    },
    parseLogs(csvData){
      const lines = csvData.split('\n');
      this.lines = lines
      this.id_logs = lines.map(item => {
        const startIndex = item.indexOf('id=') + 3;
        const endIndex = item.indexOf(',', startIndex);
        const extractedValue = item.substring(startIndex, endIndex);
        return extractedValue;
      });
    },
    getMembers(event) {
      this.headers = [];
      this.rows = [];
      
      const file = event.target.files[0];
      const reader = new FileReader();

      reader.onload = (event) => {
        const csvData = event.target.result;
        this.parseMembers(csvData);
      };

      reader.readAsText(file);
    },
    parseMembers(csvData) {
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
