<template>
  <div class="app-container">
    <el-button type="primary" @click="addGood()" style="margin-bottom:20px;">添加销售商品</el-button>
    <el-button type="primary" @click="addSale()" style="margin-bottom:20px;">保存销售单</el-button>
    <!--  -->

    <el-form label-width="98px">
      <el-row type="flex">
        <el-col :span="8">
          <el-form-item label="客 户">
            <el-input v-model="customer_id" placeholder="请输入客户名"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="销售金额">
            <el-input :disabled="true" v-model="saleAmount" placeholder="输入商品数量/单价自动计算销售金额"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="实际金额">
            <el-input v-model="actual_amount" placeholder="输入实际金额,不输入默认为销售金额"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>

    <el-table :data="tableDataList" border style="width: 100%" max-height="70vh">
      <!-- <el-table-column type="selection" width="55" align="center"></el-table-column> -->
      <el-table-column label="详情" align="center" :width="50">
        <div class="headerBox" slot="header" slot-scope="scope">
          <el-tooltip class="item" effect="dark" content="搜索" placement="top-start">
            <img src="@/assets/img/chazhao.png" alt />
          </el-tooltip>
        </div>
        <el-tooltip
          class="item"
          effect="dark"
          content="详情"
          placement="top-start"
          slot-scope="scope"
        >
          <img src="@/assets/img/tuoyuan.png" alt />
        </el-tooltip>
      </el-table-column>
      <el-table-column prop="code" align="center">
        <div class="headerBox" slot="header" slot-scope="scope">
          <el-input class="height40" v-model="querDataList.code" clearable placeholder="商品编号搜索" />
          <p>商品编号</p>
        </div>
      </el-table-column>
      <el-table-column prop="goods_name" align="center">
        <div class="headerBox" slot="header" slot-scope="scope">
          <el-input
            class="height40"
            v-model="querDataList.goods_name"
            clearable
            placeholder="商品名称搜索"
          />
          <p>商品名称</p>
        </div>
      </el-table-column>
      <el-table-column prop="specs" align="center">
        <div class="headerBox" slot="header" slot-scope="scope">
          <el-input class="height40" disabled placeholder="商品规格" />
          <p>商品规格</p>
        </div>
      </el-table-column>
      <el-table-column prop="num" align="center" label="数 量">
        <div slot-scope="scope">
          <el-input
            class="height40"
            type="number"
            v-model="scope.row.num"
            clearable
            placeholder="请输入数量"
          />
          <!-- <p>数 量</p> -->
        </div>
      </el-table-column>
      <el-table-column prop="price" align="center" label="单 价">
        <div slot-scope="scope">
          <el-input
            class="height40"
            type="number"
            v-model="scope.row.price"
            clearable
            placeholder="请输入单价"
          />
          <!-- <p>单 价</p> -->
        </div>
      </el-table-column>
      <el-table-column prop="goodAmount" :formatter="goodAmountReckon" align="center" label="金 额">
        <!-- <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled clearable placeholder="金额" />
            <p>金 额</p>
        </div>-->
      </el-table-column>
      <el-table-column prop="saleRemarks" align="center" label="备 注">
        <div slot-scope="scope">
          <el-input class="height40" v-model="scope.row.saleRemarks" clearable placeholder="请输入备注" />
          <!-- <p>单 价</p> -->
        </div>
      </el-table-column>
      <el-table-column label="操作" align="center">
        <div class="headerBox" slot="header" slot-scope="scope">
          <el-input class="height40" disabled placeholder="操作" />
          <p>操作</p>
        </div>
        <template slot-scope="scope">
          <!-- @click="handleEdit(scope.$index, scope.row)" -->
          <!-- @click="handleDelete(scope.$index, scope.row)" -->
          <el-button size="mini" @click="handleListDelete(scope.$index, scope.row)" type="danger">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- <div style="margin-top:10px;text-align:right">
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
    </div>-->

    <!--  -->
    <el-dialog title="添加销售商品" :visible.sync="goodsVisible" @close="goodsClose('form')" width="70%">
      <el-table
        v-loading="goodsLoading"
        @selection-change="handleSelectionChange"
        :data="tableData"
        border
        style="width: 100%"
      >
        <el-table-column type="selection" width="55" align="center"></el-table-column>
        <el-table-column label="详情" align="center" :width="50">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-tooltip class="item" effect="dark" content="搜索" placement="top-start">
              <img src="@/assets/img/chazhao.png" @click="goodsList()" alt />
            </el-tooltip>
          </div>
          <el-tooltip
            class="item"
            effect="dark"
            content="详情"
            placement="top-start"
            slot-scope="scope"
          >
            <img src="@/assets/img/tuoyuan.png" alt />
          </el-tooltip>
        </el-table-column>
        <el-table-column prop="code" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" v-model="querData.code" clearable placeholder="商品编号搜索" />
            <p>商品编号</p>
          </div>
        </el-table-column>
        <el-table-column prop="goods_name" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input
              class="height40"
              v-model="querData.goods_name"
              clearable
              placeholder="商品名称搜索"
            />
            <p>商品名称</p>
          </div>
        </el-table-column>
        <el-table-column prop="specs" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled placeholder="商品规格" />
            <p>商品规格</p>
          </div>
        </el-table-column>
        <el-table-column prop="price" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled clearable placeholder="指导价" />
            <p>指导价</p>
          </div>
        </el-table-column>
        <el-table-column prop="stock" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled clearable placeholder="商品库存" />
            <p>商品库存</p>
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
      </el-table>

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
      <span slot="footer" class="dialog-footer">
        <el-button @click="goodsVisible = false">取 消</el-button>
        <el-button
          type="primary"
          :disabled="selectionData.length==0"
          @click="selectedGoods"
        >已选择商品 {{selectionData.length}} 确定添加</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
// import { getList } from "@/api/table";
import request from "@/utils/request";
export default {
  name: "SaleAdd",
  data() {
    return {
      tableData: [],
      tableDataList: [],
      pageInfo: {},
      userData: [],
      goodsLoading: false,
      goodsLoadingList: false,
      querData: {},
      querDataList: {},
      goodsVisible: false,
      selectionData: [],
      customer_id: "",
      sale_amount: null,
      actual_amount: null
    };
  },
  created() {},
  methods: {
    //表格行为操作
    addGood() {
      this.getUserInfo();
      this.goodsList();
      this.goodsVisible = true;
    },

    goodsList() {
      this.goodsLoading = true;
      request({
        url: "/goods",
        method: "get",
        params: this.querData
      })
        .then(res => {
          this.tableData = res.data.list;
          this.pageInfo = res.data.pageInfo;
          this.goodsLoading = false;
          console.log(">>>>>>>>>>>", res);
        })
        .catch(err => {
          this.$message.error(err);
        });
    },
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
    handleSizeChange(val) {
      this.pageInfo.pageSize = val;
      this.querData.pageSize = val;
      this.goodsList();
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.pageInfo.pageNo = val;
      this.querData.currentPage = val;
      this.goodsList();
      console.log(`当前页: ${val}`);
    },
    goodsClose(fromName) {
      this.goodsVisible = false;
    },
    handleSelectionChange(val) {
      this.selectionData = val;
    },
    selectedGoods() {
      for (let i = 0; i < this.selectionData.length; i++) {
        this.$set(this.selectionData[i], "num", null);
        this.$set(this.selectionData[i], "price", null);
        this.$set(this.selectionData[i], "saleRemarks", "");
        this.$set(this.selectionData[i], "oid", this.selectionData[i]._id);
      }
      this.tableDataList = this.selectionData;
      this.goodsVisible = false;
    },
    goodAmountReckon(row, column, cellValue, index) {
      console.log("<<<<<<", row, cellValue);
      //   this.saleAmount;
      return row.num * row.price;
    },
    handleListDelete(index, row) {
      console.log(">>>>>>>>", index, row);
      this.$confirm("此操作将删除该列表的此商品, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.selectionData.splice(index, 1);
          this.$message({
            type: "success",
            message: "删除成功!"
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    addSale() {
      if (this.customer_id == "") {
        this.$message({
          type: "info",
          message: "请填写客户!"
        });
        return
      }
      if(this.selectionData.length==0){
          this.$message({
            type: "info",
            message: "请添加销售商品"
          });
          return;
      }else{
      for (let i = 0; i < this.selectionData.length; i++) {
        if (!this.selectionData[i].num || !this.selectionData[i].price) {
          this.$message({
            type: "info",
            message: "第" + (i + 1) + "行请完整填写数量及单价!"
          });
          return;
        }
      }}
      if (!this.actual_amount) {
        this.$confirm("您未填写实际金额, 是否确认使用销售金额?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
          .then(() => {
            // this.selectionData.splice(index, 1);
            this.actual_amount = this.sale_amount
            this.salesIssue()
          })
          .catch(() => {
            this.$message({
              type: "info",
              message: "已取消删除"
            });
            return
          });
      }else {
          this.salesIssue()
      }
    },
    salesIssue(){
        let saleForm = {
            goods_info:this.selectionData,
            actual_amount:this.actual_amount,
            customer_id:this.customer_id,
        }
        console.log('djjj',saleForm)
        request({
              url: "/sale/add",
              method: "post",
              data: saleForm
            })
              .then(res => {
                console.log("jjjjjjj", res);
                this.$router.go(-1);
                this.$message.success("销售单添加成功！");
                
              })
              .catch(err => {
                console.log('errr',err)
                this.$message.error(err);
              });
    }
  },
  computed: {
    saleAmount: function() {
      this.sale_amount = null;
      for (let i = 0; i < this.selectionData.length; i++) {
        this.sale_amount +=
          this.selectionData[i].num * this.selectionData[i].price;
      }
      console.log("ddddddd", this.sale_amount);
      return this.sale_amount;
    }
  }
};
</script>
