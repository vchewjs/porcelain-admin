<template>
  <div class="app-container">
    <el-button type="primary" @click="saleAdd()">销售开单</el-button>
    <el-button type="danger" :disabled="selectDataPrint.length!=1" @click="salePrint()">打印销售单</el-button>
    <div style="margin-top:20px">
      <el-table
        v-loading="saleLoading"
        :data="tableDataList"
        @selection-change="handleSelectionChange"
        border
        height="72vh"
        style="width: 100%"
      >
        <el-table-column type="selection" width="55" align="center"></el-table-column>
        <el-table-column label="详情" align="center" :width="50">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-tooltip class="item" effect="dark" content="搜索" placement="top-start">
              <img src="@/assets/img/chazhao.png" @click="saleList()" alt />
            </el-tooltip>
          </div>
          <el-tooltip
            class="item"
            effect="dark"
            content="详情"
            placement="top-start"
            slot-scope="scope"
          >
            <img src="@/assets/img/tuoyuan.png" @click="seeDetail(scope.row)" alt />
          </el-tooltip>
        </el-table-column>
        <el-table-column prop="billNo" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" v-model="querData.billNo" clearable placeholder="单据编号搜索" />
            <p>单据编号</p>
          </div>
        </el-table-column>
        <el-table-column prop="customer_id" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" v-model="querData.customer_id" clearable placeholder="客户搜索" />
            <p>客户</p>
          </div>
        </el-table-column>
        <el-table-column prop="actual_amount" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled placeholder="销售金额" />
            <p>开单金额</p>
          </div>
        </el-table-column>
        <el-table-column prop="creator" :formatter="creatorName" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-select v-model="querData.creator" clearable placeholder="请选择">
              <el-option
                v-for="item in userData"
                :key="item._id"
                :label="item.name"
                :value="item._id"
              ></el-option>
            </el-select>
            <!-- <el-input class="height40" v-model="querData.role" clearable placeholder="创建人" /> -->
            <p>创建人</p>
          </div>
        </el-table-column>
        <el-table-column prop="order_status" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled clearable placeholder="打印状态" />
            <p>打印状态</p>
          </div>
        </el-table-column>
        <el-table-column prop="createdAt" :formatter="createdAtime" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled clearable placeholder="创建时间" />
            <p>创建时间</p>
          </div>
        </el-table-column>
        <el-table-column label="操作" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled placeholder="操作" />
            <p>操作</p>
          </div>
          <template slot-scope="scope">
            <!-- @click="handleEdit(scope.$index, scope.row)" -->
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <!-- @click="handleDelete(scope.$index, scope.row)" -->
            <el-button size="mini" @click="handleDelete(scope.$index, scope.row)" type="danger">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div style="margin-top:10px;text-align:right">
      <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pageInfo.pageNo"
        :page-sizes="[10, 20, 30, 50]"
        :page-size="pageInfo.pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="pageInfo.total"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
import request from "@/utils/request";
var moment = require("moment");
export default {
  data() {
    return {
      saleLoading: false,
      tableDataList: [],
      pageInfo: {
        pageNo: 1,
        pageSize: 10,
        total: 0
      },
      querData: {},
      userData: [],
      selectDataPrint:[]
    };
  },
  watch: {},
  created() {
    this.getUserInfo();
    this.saleList();
  },
  methods: {
    saleAdd() {
        this.$router.push({
          name: 'SaleAdd'
        })
    },
    saleList() {
      request({
        url: "/sale/list",
        method: "get",
        params: this.querData
      })
        .then(res => {
          this.tableDataList = res.data.list;
          this.pageInfo = res.data.pageInfo
          console.log("jjwwj", res);
        })
        .catch(err => {
          this.$message.error(err);
        });
    },
    getUserInfo() {
      request({
        url: "/user/list",
        method: "get",
        params: { isPaging: 1 }
      })
        .then(res => {
          this.userData = res.data.list;
          console.log("jjwwj", res);
        })
        .catch(err => {
          this.$message.error(err);
        });
    },
    seeDetail() {},
    handleEdit(index, row) {},
    handleDelete(index, row) {},
    handleSizeChange(val) {},
    handleCurrentChange(val) {},
    creatorName(row, column, cellValue, index) {
      console.log(cellValue, ">>>>");
      let name = cellValue;
      for (let i = 0; i < this.userData.length; i++) {
        if (cellValue == this.userData[i]._id) {
          name = this.userData[i].name;
        }
      }
      // this.userData.map(x.user_id=>)
      return name;
    },
    createdAtime(row, column, cellValue, index) {
        console.log('dddd',cellValue)
      return moment(cellValue).format('YYYY-MM-DD HH:mm:ss')
    },
    handleSelectionChange(val){
        this.selectDataPrint = val
    },
    salePrint(){

    }
  }
};
</script>

