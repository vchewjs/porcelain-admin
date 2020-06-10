<template>
  <div class="app-container">
    <el-button type="primary" @click="addUser()">新 增</el-button>
    <div style="margin-top:20px">
      <el-table v-loading="userLoading" :data="tableData" border height="70vh" style="width: 100%">
        <!-- <el-table-column type="selection" width="55"></el-table-column> -->
        <el-table-column label="详情" align="center" :width="50">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-tooltip class="item" effect="dark" content="搜索" placement="top-start">
              <img src="@/assets/img/chazhao.png" @click="userList()" alt />
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
        <el-table-column prop="user_name" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" v-model="querData.user_name" clearable placeholder="账号" />
            <p>账号</p>
          </div>
        </el-table-column>
        <el-table-column prop="name" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" v-model="querData.name" clearable placeholder="姓名" />
            <p>姓名</p>
          </div>
        </el-table-column>
        <el-table-column prop="age" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled placeholder="年龄" />
            <p>年龄</p>
          </div>
        </el-table-column>
        <el-table-column prop="role" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" v-model="querData.role" clearable placeholder="角色" />
            <p>角色</p>
          </div>
        </el-table-column>
        <el-table-column label="操作" align="center">
          <div class="headerBox" slot="header" slot-scope="scope">
            <el-input class="height40" disabled placeholder="操作" />
            <p>操作</p>
          </div>
          <template slot-scope="scope">
            <!-- @click="handleEdit(scope.$index, scope.row)" -->
            <el-button size="mini">编辑</el-button>
            <!-- @click="handleDelete(scope.$index, scope.row)" -->
            <el-button size="mini" type="danger">删除</el-button>
          </template>
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
    </div>
    <el-dialog
      :title="userType==0?'新增用户':'查看用户信息'"
      :visible.sync="userVisible"
      @close="userClose('form')"
      width="30%"
    >
      <el-form ref="form" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="姓 名" prop="name">
          <el-input :disabled="userType==1" v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="账 号" prop="user_name">
          <el-input :disabled="userType==1" v-model="form.user_name"></el-input>
        </el-form-item>
        <el-form-item v-if="userType!==1" label="密 码" prop="password">
          <el-input :disabled="userType==1" v-model="form.password" show-password></el-input>
        </el-form-item>
        <el-form-item label="年 龄" prop="age">
          <el-input :disabled="userType==1" v-model="form.age"></el-input>
        </el-form-item>
        <el-form-item label="角 色" prop="role">
          <el-select :disabled="userType==1" v-model="form.role" placeholder="请选择角色">
            <el-option label="管理员" value="管理员"></el-option>
            <el-option label="开票员" value="开票员"></el-option>
            <el-option label="经办员" value="经办员"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="备 注">
          <el-input :disabled="userType==1" v-model="form.description"></el-input>
        </el-form-item>
        <el-form-item style="text-align:right">
          <el-button @click="userClose('form')">取 消</el-button>
          <el-button type="primary" v-if="userType!==1" @click="submitForm('form')">立即创建</el-button>
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
import request from "@/utils/request";
import { getToken, setToken, removeToken } from "@/utils/auth";
export default {
  name: "User",
  data() {
    var checkUserName = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("账号不能为空"));
      }
      if (4 < value.length < 12) {
      } else {
        return callback(new Error("账号长度在4-12位"));
      }
      setTimeout(() => {
        if (!/^[a-z]+$/.test(value)) {
          callback(new Error("账号只能为小写字母"));
        } else {
          callback();
        }
      }, 1000);
    };
    return {
      tableData: [],
      pageInfo: {},
      querData: {},
      userType: 0,
      currentPage4: 4,
      userVisible: false,
      userLoading: false,
      form: {
        name: "",
        user_name: "",
        age: "",
        password: "",
        role: "",
        description: ""
      },
      rules: {
        name: [
          { required: true, message: "请输入姓名", trigger: "blur" },
          { min: 2, max: 4, message: "长度在 2 到 4 个字", trigger: "blur" }
        ],
        user_name: [
          { required: true, validator: checkUserName, trigger: "blur" }
        ],
        age: [{ required: true, message: "请输入年龄", trigger: "blur" }],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 6, max: 18, message: "长度在 6 到 18 个字符", trigger: "blur" }
        ],
        role: [{ required: true, message: "请选择角色", trigger: "blur" }]
      }
    };
  },
  created() {
    this.userList();
  },
  methods: {
    addUser() {
      this.userType = 0;
      this.userVisible = true;
    },
    addSubmit() {
      console.log("submit!");
    },
    userList() {
      this.userLoading = true;
      request({
        url: "/user/list",
        method: "get",
        params: this.querData
      })
        .then(res => {
          this.tableData = res.data.list;
          this.pageInfo = res.data.pageInfo;
          this.userLoading = false;
          console.log("jjjjjjj", res);
        })
        .catch(err => {
          this.$message.error(err);
        });
    },
    handleSizeChange(val) {
      this.pageInfo.pageSize = val;
      this.querData.pageSize = val;
      this.userList();
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.pageInfo.pageNo = val;
      this.querData.currentPage = val;
      this.userList();
      this.console.log(`当前页: ${val}`);
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          // alert("submit!");
          request({
            url: "/user/add",
            method: "post",
            headers: {
              Authorization: "Bearer " + getToken()
            },
            data: this.form
          })
            .then(res => {
              console.log("jjjjjjj", res);
              this.$message.success("用户添加成功！");
              // this.$refs[formName].resetFields();
              this.userVisible = false;
              this.form = {
                name: "",
                user_name: "",
                age: "",
                password: "",
                role: "",
                description: ""
              };
              this.userList();
            })
            .catch(err => {
              this.$message.error(err);
            });
        } else {
          // console.log("error submit!!");
          this.$message("error 提交失败！");
          return false;
        }
      });
    },
    userClose(formName) {
      // this.$refs[formName].resetFields();
      this.userVisible = false;
      this.form = {
        name: "",
        user_name: "",
        age: "",
        password: "",
        role: "",
        description: ""
      };
    },
    seeDetail(row) {
      this.userType = 1;
      this.form = row;
      this.userVisible = true;
      console.log("row >>>>>>>>", row);
    }
  }
};
</script>

<style scoped>
</style>

