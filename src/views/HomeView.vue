<template>
    <el-container style="height: 500px; height: 100%">
      <el-aside width="sidewith+'px'" style="background-color: rgb(238, 241, 246);height: 100% ;">
        <el-menu :default-openeds="['1', '3']" style="min-height: 100vh;overflow-x: hidden"
         background-color="rgb(45,65,86)"
                 text-color="#fff"
                 active-text-color="#ffd04b"
                 :collapse-transition="false"
                 :collapse="isCollapse"
        >
          <div style="height: 60px;line-height: 60px;text-align: center">
            <img src="../assets/logo2.jpg" style="width: 40px;position: relative;top:15px;margin-right: 3px">
            <b style="color:skyblue" v-show="logoshow">港运达车队系统</b>
          </div>
          <el-submenu index="1" style="width: 200px">
            <template slot="title">
              <i class="el-icon-user-solid"></i>
              <span slot="title">用户管理</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="1-1">用户查询</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-submenu index="2" style="width: 200px">
            <template slot="title">
              <i class="el-icon-truck"></i>
              <span slot="title">车辆管理</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="2-1">车辆查询</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-submenu index="3" style="width: 200px">
            <template slot="title">
              <i class="el-icon-date"></i>
              <span slot="title">货单记录</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="3-1">货单查询</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>

      <el-container>

        <el-header style=" font-size: 12px;border-bottom: 1px solid #ccc;line-height: 60px;display: flex">
          <div style="flex: 1px;font-size: 18px">
            <span :class="collapseBtnClass" style="cursor: pointer" @click="collapse"></span>

          </div>

          <el-dropdown style="width: 70px ;cursor: pointer">
            <span>辛文波</span><i class="el-icon-caret-bottom" style="margin-left: 5px"></i>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>个人信息</el-dropdown-item>
              <el-dropdown-item>退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>

        </el-header>

        <el-main>
          <el-breadcrumb separator-class="el-icon-arrow-right" style="margin-bottom: 30px">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>货单查询</el-breadcrumb-item>
          </el-breadcrumb>
          <div style="margin: 5px 0;">
            <el-input style="width: 200px;" placeholder="请输入单号" suffix-icon="el-icon-search"></el-input>
            <el-button class="ml-5" type="primary">搜索</el-button>
            <el-time-picker style="margin-left: 50px;height: 45px"
                v-model="value1"
                :picker-options="{
      selectableRange: '18:30:00 - 20:30:00'
    }"
                placeholder="开始时间">
            </el-time-picker>
            <b>---</b>
            <el-time-picker style="height: 45px"
                arrow-control
                v-model="value2"
                :picker-options="{
      selectableRange: '18:30:00 - 20:30:00'
    }"
                placeholder="结束时间">
            </el-time-picker>
            <el-button class="ml-5 el-icon-search" type="primary" ></el-button>
            <div style="margin: 10px 0">
              <el-button type="primary"><i class="el-icon-circle-plus-outline"></i>新增</el-button>
              <el-button type="danger"><i class="el-icon-remove-outline"></i>批量删除</el-button>
              <el-button type="primary"><i class="el-icon-upload"></i>导入</el-button>
              <el-button type="primary"><i class="el-icon-document"></i>导出EXCEL</el-button>
            </div>

          </div>
          <el-table :header-cell-class-name="headerBg"
              :data="tableData"
              border
              height="200"
              :summary-method="getSummaries"
              show-summary
              style="width: 100%; margin-top: 20px"
            @selection-change="handleSelectionChange">
            <el-table-column
                type="selection"
                width="55">
            </el-table-column>
            <el-table-column
                prop="id"
                label="车牌号"
                width="180">
            </el-table-column>
            <el-table-column
                prop="name"
                label="姓名">
            </el-table-column>
            <el-table-column
                prop="amount1"
                label="数值 1（元）">
            </el-table-column>
            <el-table-column
                prop="amount2"
                label="数值 2（元）">
            </el-table-column>
            <el-table-column
                prop="amount3"
                label="数值 3（元）">
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button
                    type="success"
                    @click="handleEdit(scope.$index, scope.row)">编辑<i class="el-icon-edit"></i></el-button>
                <el-button
                    type="danger"
                    @click="handleDelete(scope.$index, scope.row)">删除<i class="el-icon-remove-outline"></i></el-button>
              </template>
            </el-table-column>
          </el-table>
          <div style="padding: 10px 0">
            <el-pagination
                :page-sizes="[10, 20, 30, 40]"
                :page-size="10"
                layout="total, sizes, prev, pager, next, jumper"
                :total="400">
            </el-pagination>
          </div>
        </el-main>
      </el-container>
    </el-container>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  data(){
    const item = {
      date: '2016-05-02',
      name: '辛文波',
      address: '上海市普陀区金沙江路 1518 弄',
      //时间输入框
      value1: new Date(2016, 9, 10, 18, 40),
      value2: new Date(2016, 9, 10, 18, 40),
        };
    return {
      //主页面数据
      tableData: [{
        id: '12987122',
        name: '王小虎',
        amount1: '234',
        amount2: '3.2',
        amount3: 10
      }],
      collapseBtnClass:"el-icon-s-fold",
      isCollapse:false,
      sidewith:200,
      logoshow:true,
      //头部背景色
      headerBg:'headerBg',
      multipleSelection: []

    }
  },
  methods:{
    //侧边栏收缩
    collapse(){
      this.isCollapse=!this.isCollapse
      if(this.isCollapse){
        this.sidewith=64
        this.collapseBtnClass="el-icon-s-unfold"
        this.logoshow=false
      }else {
        this.sidewith=200
        this.collapseBtnClass="el-icon-s-fold"
        this.logoshow=true
      }
    },

    //获取总金额
    getSummaries(param) {
      const { columns, data } = param;
      const sums = [];
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = '合计';
          return;
        }
        const values = data.map(item => Number(item[column.property]));
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);
            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          sums[index] += ' 元';
        } else {
          sums[index] = 'N/A';
        }
      });

      return sums;
    },

    //操作按钮控制
    handleEdit(index, row) {
      console.log(index, row);
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    //多行选择
    toggleSelection(rows) {
      if (rows) {
        rows.forEach(row => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    }
  }
}
</script>
<style>
.headerBg{
  background:aquamarine !important;
}
</style>
