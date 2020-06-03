<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: "App"
};
</script>
<style>
.el-table th > .cell {
  padding: 0 !important;
  min-height: 40px !important;
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
}
.el-table--border,
.el-table--group {
  border: 1px solid #dcdfe6 !important;
}
.el-table td,
.el-table th.is-leaf {
  border-bottom: 1px solid #dcdfe6 !important;
}
.el-table--border td,
.el-table--border th,
.el-table__body-wrapper .el-table--border.is-scrolling-left ~ .el-table__fixed {
  border-right: 1px solid #dcdfe6 !important;
}
.el-table th {
  padding: 0 !important;
}
.el-table th {
  background-color: #f5f7fa !important;
}
.headerBox {
  width: 100%;
  display: flex !important;
  flex-direction: column !important;
  justify-content: center !important;
  align-items: center !important;
  padding: 0 !important;
}
.headerBox .el-input__inner {
  border: none !important;
  width: 100% !important;
  height: 40px !important;
  text-align: center !important;
  padding: 0px 1px !important;
}
.headerBox p,
.cell {
  /* margin: 0 !important; */
  color: #333 !important;
}
.headerBox .el-input,
.headerBox .el-select,
.headerBox .el-date-picker {
  padding: 0 !important;
  width: 100% !important;
  border-bottom: 1px solid #dcdfe6 !important;
}
</style>
