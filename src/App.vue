<template>
  <div>
    <div>
      <input type="file" @change="convert($event)" />
    </div>
    <div id="output">
      <table>
        <tr>
          <th>isim</th>
          <th>soyisim</th>
          <th>yaş</th>
          <th>cinsiyet</th>
          <th>meslek</th>
          <th>maaş</th>
        </tr>
        <tr v-for="(d, i) in data" :key="i">
          <td>{{ d.İsim }}</td>
          <td>{{ d.Soyisim }}</td>
          <td>{{ d.Yaş }}</td>
          <td>{{ d.Cinsiyet }}</td>
          <td>{{ d.Meslek }}</td>
          <td>{{ d.Maaş }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import XLSX from 'xlsx';

export default {
  name: 'App',
  data() {
    return {
      data: []
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
        // const finalJsonData = JSON.stringify(rowObject, undefined, 4);
        vm.data = rowObject;
      };
      reader.readAsBinaryString(e.target.files[0]);
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
