<template>
  <div class="m-view-uploadExcel"
    v-loading="loading">
    <el-upload class="upload"
      action=""
      :multiple="false"
      :auto-upload="false"
      :file-list="fileList"
      :accept="acceptFileType"
      :on-success="uploadSuccess"
      :on-change="fileChange"
      :limit="1"
      name="fileObject"
      ref="upload">
      <el-button slot="trigger"
        type="primary">选取文件</el-button>
      <el-button type="primary"
        @click="submitUpload">上传</el-button>
      <div slot="tip"
        class="el-upload__tip">只能上传xlsx/xls文件</div>
      <el-button type="primary"
        @click="downExample">下载模板</el-button>
    </el-upload>
    <div style="max-height:300px;overflow-y:scroll;">
      <p v-for="(item,index) in errorRow"
        :key="index"
        style="color:red">
        {{item}}
      </p>
    </div>

  </div>
</template>
<script>
import XLSX from "xlsx";
export default {
  data() {
    return {
      fileList: [],
      acceptFileType: ".xlsx,.xls",
      sheet: [],
      loading: false,
      data: ["枚举编码,枚举名称,上级,是否叶节点,排序,备注".split(",")]
    };
  },
  props: {
    errorRow: {}
  },
  methods: {
    submitUpload() {
      if (this.sheet.length == 0) {
        this.errorRow = ["没有可导入数据"];
        return;
      }
      this.$emit("summitUpload", this.sheet);
    },
    uploadSuccess() {},
    fileChange(item) {
      this.loading = true;
      const fileObj = item.raw;
      let reader = new FileReader();
      reader.readAsBinaryString(fileObj);
      let _this = this;
      reader.onload = function(evt) {
        var data = evt.target.result;
        var workbook = XLSX.read(data, { type: "binary" });
        _this.sheet = XLSX.utils.sheet_to_json(workbook.Sheets.Sheet1);
        _this.loading = false;
      };
    },
    downExample() {
      /* convert state to workbook */
      const ws = XLSX.utils.aoa_to_sheet(this.data);
      const wb = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(wb, ws, "Sheet1");
      /* generate file and send to client */
      XLSX.writeFile(wb, "枚举导入模板.xlsx");
    }
  }
};
</script>
<style>
</style>
