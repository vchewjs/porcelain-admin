<template>
  <div class="app-container">
    <el-button type="primary" @click="addGood()">新 增</el-button>
    <div style="margin-top:20px">
      <el-table v-loading="goodsLoading" :data="tableData" border height="72vh" style="width: 100%">
        <!-- <el-table-column type="selection" width="55"></el-table-column> -->
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
            <img src="@/assets/img/tuoyuan.png" @click="seeDetail(scope.row)" alt />
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
    <el-dialog
      :title="goodsNewTitle+'商品'"
      :visible.sync="goodsVisible"
      @close="goodsClose('form')"
      width="40%"
    >
      <el-form ref="form" :model="form" :rules="rules" label-width="85px">
        <el-form-item label="商品编号" prop="code">
          <el-input :disabled="goodsType==1" placeholder="请输入商品编号，例：0101015 " v-model="form.code"></el-input>
        </el-form-item>
        <el-form-item label="商品名称" prop="goods_name">
          <el-input
            :disabled="goodsType==1"
            placeholder="请输入商品名称，例：2号底瓦175mm*175mm/青灰色 "
            v-model="form.goods_name"
          ></el-input>
        </el-form-item>
        <el-form-item label="规格/单位" prop="specs">
          <el-input :disabled="goodsType==1" placeholder="请输入商品规格/单位，例：片 " v-model="form.specs"></el-input>
        </el-form-item>
        <el-form-item label="指导价" prop="price">
          <el-input :disabled="goodsType==1" placeholder="请输入商品指导价 " v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="商品库存">
          <el-input :disabled="goodsType==1" placeholder="请输入商品库存 " v-model="form.stock"></el-input>
        </el-form-item>
        <el-form-item label="备 注">
          <el-input :disabled="goodsType==1" placeholder="请输入备注 " v-model="form.remarks"></el-input>
        </el-form-item>
        <el-form-item style="text-align:right">
          <el-button @click="goodsClose('form')">取 消</el-button>
          <el-button type="warning" v-if="goodsType==0" plain @click="resetForm('form')">重置</el-button>
          <el-button
            type="primary"
            v-if="goodsType!==1"
            @click="submitForm('form')"
          >立即{{goodsNewTitle}}</el-button>
        </el-form-item>
      </el-form>

      <!-- <span slot="footer" class="dialog-footer">
        <el-button @click="userVisible = false">取 消</el-button>
        <el-button type="primary">确 定</el-button>
      </span>-->
    </el-dialog>
  </div>
</template>

<script>
// import { getList } from "@/api/table";
import request from "@/utils/request";
export default {
  name: "Goods",
  data() {
    return {
      tableData: [],
      pageInfo: {},
      userData: [],
      goodsLoading: false,
      querData: {},
      goodsNewTitle: "新增",
      goodsType: 0,
      goodsVisible: false,
      form: {
        code: "", //商品编码
        goods_name: "", //商品名
        specs: "", //商品规格
        price: "", //指导价
        stock: "", //商品库存
        remarks: "" //备注
      },
      rules: {
        code: [{ required: true, message: "请输入商品编号", trigger: "blur" }],
        goods_name: [
          { required: true, message: "请输入商品名称", trigger: "blur" }
        ],
        specs: [
          { required: true, message: "请输入商品规格/单位", trigger: "blur" }
        ],
        price: [{ required: true, message: "请输入指导价", trigger: "blur" }]
      }
    };
  },
  created() {
    this.getUserInfo();
    this.goodsList();
  },
  methods: {
    //表格行为操作
    addGood() {
      this.goodsType = 0;
      this.goodsNewTitle = "新增";
      this.goodsVisible = true;
    },
    handleEdit(index, row) {
      this.goodsVisible = true;
      this.goodsType = 2;
      this.goodsNewTitle = "修改";
      this.form = row;
      console.log(">>>>>index>>>row", index, row);
    },
    handleDelete(index, row) {
      this.$confirm("此操作将永久删除该条数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          request({
            url: "/goods/" + row._id,
            method: "post"
          })
            .then(res => {
              if (res.code == 200 && res.msg == "success") {
                this.$message({
                  type: "success",
                  message: "删除成功!"
                });
                this.goodsList();
              }

              console.log(">>>>>>>>>>>", res);
            })
            .catch(err => {
              this.$message.error(err);
            });
          // /goods/012244455567778889abcdee;
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
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
    seeDetail(row) {
      this.form = row;
      this.goodsType = 1;
      this.goodsNewTitle = "查看";
      this.goodsVisible = true;
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
      this.form = {};
    },
    submitForm(fromName) {
      this.$refs[fromName].validate(valid => {
        if (valid) {
          console.log("okokok");
          if (this.goodsType == 0) {
            request({
              url: "/goods/add",
              method: "post",
              data: this.form
            })
              .then(res => {
                console.log("jjjjjjj", res);
                this.$message.success("商品添加成功！");
                // this.$refs[formName].resetFields();
                this.goodsVisible = false;
                this.form = {
                  code: "", //商品编码
                  goods_name: "", //商品名
                  specs: "", //商品规格
                  price: "", //指导价
                  stock: "", //商品库存
                  remarks: "" //备注
                };
                this.goodsList();
              })
              .catch(err => {
                console.log('errr',err)
                this.$message.error(err);
              });
          } else if (this.goodsType == 2) {
            console.log("oooooooo", this.form);
            request({
              url: "/goods/update/" + this.form._id,
              method: "post",
              data: this.form
            })
              .then(res => {
                console.log("jjjjjjj", res);
                this.$message.success("商品修改成功！");
                // this.$refs[formName].resetFields();
                this.goodsVisible = false;
                this.form = {
                  code: "", //商品编码
                  goods_name: "", //商品名
                  specs: "", //商品规格
                  price: "", //指导价
                  stock: "", //商品库存
                  remarks: "" //备注
                };
                this.goodsList();
              })
              .catch(err => {
                this.$message.error(err);
              });
          }
        } else {
          console.log("error");
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>
