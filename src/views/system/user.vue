/**
* 系统管理 用户管理
*/
<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索筛选 -->
    <el-form :inline="true" class="user-search">
      <el-form-item label="搜索："></el-form-item>
      <el-form-item label="">
        <el-input size="small" v-model="search" placeholder="输入用户名"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button size="small" type="primary" icon="el-icon-plus" @click="addFormVisible = true">添加</el-button>
        <el-button size="small" type="primary" icon="el-icon-plus" @click="handleEdit()">修改</el-button>
      </el-form-item>
    </el-form>
    <!--列表-->
    <el-table size="small" @selection-change="selectChange"
              :data="listData.filter(data => !search || data.name.includes(search))" highlight-current-row
              style="width: 100%;">
      <el-table-column align="center" type="selection" width="50">
      </el-table-column>
      <el-table-column align="center" sortable prop="name" label="用户名" width="120">
      </el-table-column>
      <el-table-column align="center" sortable prop="password" label="密码" width="120">
      </el-table-column>
      <el-table-column align="center" sortable prop="provinceName" label="省份" width="120">
      </el-table-column>
      <el-table-column align="center" sortable prop="cityName" label="城市" width="120">
      </el-table-column>
      <el-table-column align="center" sortable prop="contribute" label="贡献值" min-width="50">
      </el-table-column>
      <el-table-column align="center" sortable prop="phoneNumber" label="电话" min-width="120">
      </el-table-column>
      <el-table-column align="center" sortable prop="editTime" label="修改时间" min-width="120">
        <template slot-scope="scope">
          <div>{{ scope.row.editTime|timestampToTime }}</div>
        </template>
      </el-table-column>
      <el-table-column align="center" sortable prop="permission" label="权限" min-width="50">
      </el-table-column>
      <el-table-column align="center" prop="address" label="详细地址" min-width="300"></el-table-column>
    </el-table>

    <el-dialog title="添加用户" :visible.sync="addFormVisible" width="30%" @click='this.addFormVisible = false'>
      <el-form :model="addForm" :rules="rules">
        <el-form-item label="姓名" prop="add_name">
          <el-input v-model="addForm.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="add_password">
          <el-input v-model="addForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="省份" prop="add_provinceName">
          <el-input v-model="addForm.provinceName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="城市" prop="add_cityName">
          <el-input v-model="addForm.cityName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="详细地址" prop="add_address">
          <el-input v-model="addForm.address" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话号码" prop="add_phoneNumber">
          <el-input v-model="addForm.phoneNumber" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="权限" prop="add_permission">
          <el-select v-model="addForm.permission" placeholder="请选择活动区域">-->
            <el-option label="-1" value="-1"></el-option>
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="贡献值" prop="add_contribute">
          <el-input v-model="addForm.contribute" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" type="primary" @click="addUser()">保存</el-button>
      </div>
    </el-dialog>

    <el-dialog title="修改用户信息" :visible.sync="editFormVisible" width="30%" @click='this.editFormVisible = false'>
      <el-form :model="editForm">
        <el-form-item label="姓名">
          <el-input v-model="editForm.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="editForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="省份">
          <el-input v-model="editForm.provinceName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="城市">
          <el-input v-model="editForm.cityName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="详细地址">
          <el-input v-model="editForm.address" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话号码">
          <el-input v-model="editForm.phoneNumber" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="权限">
          <el-select v-model="editForm.permission" placeholder="请选择活动区域">-->
            <el-option label="-1" value="-1"></el-option>
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="贡献值">
          <el-input v-model="editForm.contribute" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" type="primary" @click="editUser()">保存</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
// 导入请求方法
import Pagination from '../../components/Pagination'
import axios from "axios";

export default {
  data() {
    return {
      nshow: true, //switch开启
      fshow: false, //switch关闭
      addFormVisible: false, // 控制添加用户界面显示与隐藏
      editFormVisible: false, //控制编辑页面显示与隐藏
      search: '',
      selectData: '',
      selectDataLength: '',
      listData: {
        "name": "",
        "password": "",
        "provinceName": "",
        "cityName": "",
        "address": "",
        "phoneNumber": "",
        "permission": "",
        "contribute": ""
      },
      addForm: {
        "name": "",
        "password": "",
        "provinceName": "",
        "cityName": "",
        "address": "",
        "phoneNumber": "",
        "permission": "",
        "contribute": ""
      },
      // 编辑
      editForm: {
        "name": "",
        "password": "",
        "provinceName": "",
        "cityName": "",
        "address": "",
        "phoneNumber": "",
        "permission": "",
        "contribute": ""
      },

      // rules表单验证
      rules: {
        add_name: [{required: true, message: '请输入账号', trigger: 'blur'}],
        add_password: [{required: true, message: '请输入密码', trigger: 'blur'}],
        add_provinceName: [{required: true, message: '请输入省份名称', trigger: 'blur'}],
        add_cityName: [{required: true, message: '请输入城市名称', trigger: 'blur'}],
        add_address: [{required: true, message: '请输入详细地址', trigger: 'blur'}],
        add_phoneNumber: [{required: true, message: '请输入电话号码', trigger: 'blur'}],
        add_permission: [{required: true, message: '请选择权限值', trigger: 'blur'}],
        add_contribute: [{required: true, message: '请输入贡献值', trigger: 'blur'}],
      },
    }
  },
  // 注册组件
  components: {
    Pagination
  },

  /**
   * 数据发生改变
   */
  watch: {},
  /**
   * 创建完毕
   */
  created() {
    this.getdata()
  },
  /**
   * 里面的方法只有被调用才会执行
   */
  methods: {
    // 获取数据方法
    getdata() {
      axios.get("api/user/showAllUser").then((response) => {
        // this.userData = response.data;
        // console.log(this.userData)
        this.listData = response.data
      }, function (err) {
        console.log(err);
      })
    },

    addUser() {
      let params = new URLSearchParams();
      params.append('name', this.addForm.name)
      params.append('password', this.addForm.password)
      params.append('provinceName', this.addForm.provinceName)
      params.append('cityName', this.addForm.cityName)
      params.append('address', this.addForm.address)
      params.append('phoneNumber', this.addForm.phoneNumber)
      params.append('permission', this.addForm.permission)
      params.append('contribute', this.addForm.contribute)

      axios.post("api/user/addUser/", params, {
        headers: {
          'Access-Control-Allow-Credentials': 'true', //解决session问题
          'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8' //将表单数据传递转化为form-data类型
        }
      }).then((response) => {
        if (response.data == 'fail') {
          alert('用户已存在，请更换用户名')
        } else if (response.data == "success") {
          alert('注册成功')
          setTimeout(() => {
            this.RestartAdd()
          }, 1000)
        }
      }, function (err) {
        console.log("错误是" + err.response)
      })
    },

    RestartAdd() {
      this.addFormVisible = false
      this.addForm.password = ""
      this.addForm.provinceName = ""
      this.addForm.cityName = ""
      this.addForm.address = ""
      this.addForm.phoneNumber = ""
      this.addForm.permission = ""
      this.addForm.contribute = ""
      this.$router.push({path: '/system/user'})
    },

    RestartEdit() {
      this.editFormVisible = false
      this.editForm.password = ""
      this.editForm.provinceName = ""
      this.editForm.cityName = ""
      this.editForm.address = ""
      this.editForm.phoneNumber = ""
      this.editForm.permission = ""
      this.editForm.contribute = ""
      this.$router.push({path: '/system/user'})
    },

    selectChange(val) {
      // 此处获取到的val是一个数组
      this.selectDataLength = val.length
      if (this.selectDataLength == 1) {
        this.editForm.name = val[0].name
        this.editForm.password = val[0].password
        this.editForm.provinceName = val[0].provinceName
        this.editForm.cityName = val[0].cityName
        this.editForm.address = val[0].address
        this.editForm.phoneNumber = val[0].phoneNumber
        this.editForm.permission = val[0].permission
        this.editForm.contribute = val[0].contribute
      }
    },

    handleEdit() {
      if (this.selectDataLength == 0) {
        alert('请选中用户后再修改')
      } else if (this.selectDataLength > 1) {
        alert('对不起，一次性只能修改一个对象')
      } else {
        this.editFormVisible = true
      }
    },

    editUser() {


      let params = new URLSearchParams();
      params.append('name', this.editForm.name)
      params.append('password', this.editForm.password)
      params.append('provinceName', this.editForm.provinceName)
      params.append('cityName', this.editForm.cityName)
      params.append('address', this.editForm.address)
      params.append('phoneNumber', this.editForm.phoneNumber)
      params.append('permission', this.editForm.permission)
      params.append('contribute', this.editForm.contribute)

      axios.post("api/user/modifyUser/", params, {
        headers: {
          'Access-Control-Allow-Credentials': 'true', //解决session问题
          'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8' //将表单数据传递转化为form-data类型
        }
      }).then((response) => {
        if (response.data == 'fail') {
          alert('修改失败')
        } else if (response.data == "success") {
          alert('修改成功')
          setTimeout(() => {
            this.RestartEdit()
          }, 1000)
        }
      }, function (err) {
        console.log("错误是" + err.response)
      })
    }

  }
}
</script>

<style scoped>
.user-search {
  margin-top: 20px;
}

.userRole {
  width: 100%;
}
</style>

