<template>
  <div>
    <h1>Excel Converter</h1>
    <vue-good-table
      id="my-table"
      v-if="data"
      :columns="columns"
      :rows="data"
      :pagination-options="{
        enabled: true
      }"
      styleClass="vgt-table striped"
    >
      <div slot="table-actions">
        <label class="btn" for="file">Import</label>
        <input type="file" id="file" @change="convert($event)" />
        <button class="btn" @click="exportToExcel()">Export</button>
        <button class="btn" @click="convertToPdf()">Pdf</button>
      </div>
    </vue-good-table>
  </div>
</template>

<script>
import XLSX from 'xlsx';
import { saveAs } from 'file-saver';
import 'vue-good-table/dist/vue-good-table.css';
import { VueGoodTable } from 'vue-good-table';
import { jsPDF } from 'jspdf';

export default {
  name: 'App',
  components: {
    VueGoodTable
  },
  data() {
    return {
      data: [],
      columns: [
        {
          label: 'Full Name',
          field: 'fullName'
        },
        {
          label: 'Age',
          field: 'age'
        },
        {
          label: 'Birth Date',
          field: 'birthDate'
        },
        {
          label: 'Status',
          field: 'status'
        },
        {
          label: 'Gender',
          field: 'gender'
        },
        {
          label: 'Profession',
          field: 'profession'
        },
        {
          label: 'Salary',
          field: 'salary'
        },
        {
          label: 'Income Status',
          field: 'incomeStatus'
        }
      ]
    };
  },
  methods: {
    convert(e) {
      var reader = new FileReader();
      let vm = this;
      reader.onload = function(e) {
        var data = e.target.result;
        var workbook = XLSX.read(data, { type: 'binary' });
        let sheetName = workbook.SheetNames[0];
        let worksheet = workbook.Sheets[sheetName];
        let rowObject = XLSX.utils.sheet_to_row_object_array(worksheet);
        let mockData = rowObject.map((i) => {
          let j = {
            fullName: i.name + ' ' + i.surname,
            age: i.age,
            birthDate: i.birthDate,
            status: i.age < 18 ? 'Child' : i.age > 17 && i.age < 66 ? 'Young' : i.age > 65 && i.age < 80 ? 'Middle Aged' : 'Old',
            gender: i.gender,
            profession: i.profession,
            salary: i.salary,
            incomeStatus: parseInt(i.salary) < 5001 ? 'Low' : parseInt(i.salary) > 5000 && parseInt(i.salary) < 15001 ? 'Middle' : 'High'
          };
          return j;
        });
        vm.data = mockData;
      };
      reader.readAsBinaryString(e.target.files[0]);
    },
    exportToExcel() {
      let worksheet = XLSX.utils.json_to_sheet(this.data);
      let workbook = {
        Sheets: {
          data: worksheet
        },
        SheetNames: ['data']
      };
      let excelBuffer = XLSX.write(workbook, { bookType: 'xlsx', type: 'array' });
      let data = new Blob([excelBuffer], { type: 'xlsx' });
      saveAs(data, 'sampleExcel' + '_export_' + new Date().getTime() + '.xlsx');
    },
    convertToPdf() {
      var doc = new jsPDF();
      let new_arr = [];
      for (var i = 0, j = this.data.length; i < j; i++) {
        new_arr.push(this.data[i]);
      }
      new_arr = this.data.map((key) => Object.values(key));
      doc.autoTable({
        head: [['Full Name', 'Age', 'Birth Date', 'Status', 'Gender', 'Profession', 'Salary', 'Income Status']],
        body: new_arr
      });
      doc.save('export_table.pdf');
    }
  }
};
</script>

<style>
body {
  font-family: 'Roboto', sans-serif;
  color: #606266;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #606266;
  margin-top: 60px;
}
.btn {
  cursor: pointer;
  border: 1px solid #dcdfe6;
  padding: 10px 15px;
  background: transparent;
  font-size: 18px;
  color: #606266;
  margin-right: 20px;
  font-weight: 600;
}
input {
  display: none;
}
</style>
