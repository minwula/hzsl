<template>
  <div>
    <h2>订单管理</h2>
    <!-- <el-button type="success" size="small" round @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small" round @click="toDeleteAllHandler">批量删除</el-button> -->

    <!-- {{ activeName }}
    {{ orders }}
    {{ showList }} -->
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="所有订单" name="first">所有订单</el-tab-pane>
      <el-tab-pane label="待支付" name="second">待支付</el-tab-pane>
      <el-tab-pane label="待派单" name="third">待派单</el-tab-pane>
      <el-tab-pane label="待接单" name="fourth">待接单</el-tab-pane>
      <el-tab-pane label="待服务" name="fifth">待服务</el-tab-pane>
      <el-tab-pane label="待确认" name="sixth">待确认</el-tab-pane>
      <el-tab-pane label="已完成" name="seventh">已完成</el-tab-pane>
    </el-tabs>
    <el-table :data="showList">
      <!-- <el-table-column type="selection" /> -->

      <el-table-column prop="id" label="编号" />
      <el-table-column prop="ordertime" label="订单日期" />
      <el-table-column prop="total" label="总价" />
      <el-table-column prop="status" label="状态" />
      <el-table-column prop="costomerId" label="顾客id" />
      <el-table-column v-slot="slot" label="操作">
        <template>
          <a href="" @click.prevent="toShowHandler(slot.row.id)"> <i class="el-icon-info" /></a>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      layout="prev, pager, next"
      :total="50"
    />

    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%"
    >
      <span>编号 { orders.id } <br>
        订单日期"orderTime": 1572248274468,
        总价"total": 93,
        状态"status": "已完成",
        "remark": null,
        "customerId": 26,
        "waiterId": 29,
        "addressId": 2228</span>
      <span slot="footer" class="dialog-footer">
        <!-- <el-button size="small" @click="closeModalHandler">取 消</el-button> -->
        <el-button type="primary" size="small" @click="closeModalHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 提示框 :before-close="handleClose"-->
    <!-- <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%"
    >
      {{ form }}
      <el-form :model="form" label-width="80px">
        <el-form-item label="名称">
          <el-input v-model="form.name" />
        </el-form-item>
        <el-form-item label="价格">
          <el-input v-model="form.price" />
        </el-form-item>
        <el-form-item label="所属栏目">
          <el-select v-model="form.num" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.id"
              :label="item.name"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="介绍">
          <el-input v-model="form.description" />
        </el-form-item>
        <el-form-item label="产品主图">
          <el-input v-model="form.photo" />
        </el-form-item>

      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog> -->

  </div>
</template>

<script>
import request from '@/utils/request'
// import querystring from 'querystring'
export default {
  // data用于存放要向网页中存放的数据
  data() {
    return {
      activeName: 'first',
      visible: false,
      title: '',
      //   form: {
      //     type: 'product'
      //   },
      //   options: [],
      orders: [],
      showList: []
    }
  }, created() {
    console.log('Created')
    this.methods.loadData()
  },
  // 存放网页中需要调用的方法
  methods: {
    handleClick() {
      switch (this.activeName) {
        case 'first':
          this.loadData('')
          console.log('InSwitch')
          break
        case 'second':
          this.loadData('待支付')
          console.log('InSwitch')
          break
        case 'third':
          console.log('InSwitch')
          this.loadData('待派单')
          break
        case 'fourth':
          console.log('InSwitch')
          this.loadData('待接单')
          break
        case 'fifth':
          console.log('InSwitch')
          this.loadData('待服务')
          break
        case 'sixth':
          console.log('InSwitch')
          this.loadData('待确认')
          break
        case 'seventh':
          console.log('InSwitch')
          this.loadData('已完成')
          break
      }
    },
    loadData(status) {
      const url = 'http://localhost:6677/order/findAll'
      request.get(url).then((response) => {
        this.orders = response.data
      })
      if (status === '') {
        this.showList = this.orders
        return
      }
      this.showList = []
      this.orders.forEach((toShow) => {
        if (toShow.status === status) {
          this.showList.push(toShow)
        }
      })
    },
    // toggleSelection(rows) {
    //   if (rows) {
    //     rows.forEach(row => {
    //       this.$refs.multipleTable.toggleRowSelection(row)
    //     })
    //   } else {
    //     this.$refs.multipleTable.clearSelection()
    //   }
    // },
    // 多选删除！！！！！！！！！！！！！！！！！
    // handleSelectionChange(val) {
    //   this.delIds = val
    // },
    // toAddHandler() {
    //   this.form = {
    //     type: 'product'
    //   }
    //   const url = 'http://localhost:6677/category/findAll'
    //   request.get(url).then((response) => {
    //     this.options = response.data
    //   })
    //   this.title = '添加产品信息'
    //   this.visible = true
    // },
    closeModalHandler() {
      this.visible = false
    }, toUpdateHandler(row) {
      this.form = row
      this.visible = true
    }, toShowHandler(id) {
      this.visible = true
    //   this.$confirm('此操作将永久删除该行, 是否继续?', '提示', {
    //     confirmButtonText: '确定',
    //     cancelButtonText: '取消',
    //     type: 'warning'
    //   }).then(() => {
    //     const url = 'http://localhost:6677/product/deleteById?id=' + id
    //     request.get(url).then((response) => {
    //       this.loadData()
    //     })
    //     this.$message({
    //       type: 'success',
    //       message: '第 ' + id + ' 行删除成功!'
    //     })
    //   })
    // },
    // toDeleteAllHandler() {
    //   // 删除所有选中项！！
    //   this.delIds.forEach((object) => {
    //     this.delarr.push(object.id)
    //   })
    //   const url = 'http://localhost:6677/product/batchDelete?ids=' + this.delarr
    //   request({
    //     url,
    //     method: 'post',
    //     headers: {
    //       'Content-Type': 'application/x-www-form-urlencoded'
    //     },
    //     data: querystring.stringify(this.form)
    //   }).then((response) => {
    //     this.loadData()
    //     this.$message({
    //       type: 'success',
    //       message: response.message
    //     })
    //   })
    }
    // ,
    // submitHandler() {
    //   const url = 'http://localhost:6677/product/saveOrUpdate'
    //   request({
    //     url,
    //     method: 'post',
    //     headers: {
    //       'Content-Type': 'application/x-www-form-urlencoded'
    //     },
    //     data: querystring.stringify(this.form)
    //   }).then((response) => {
    //     this.loadData()
    //     this.$message({
    //       type: 'success',
    //       message: response.message
    //     })
    //     this.closeModalHandler()
    //   })
    // }
  }
}
</script>

<style scoped>

</style>
