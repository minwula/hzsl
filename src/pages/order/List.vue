<template>
  <div>
    <h2>订单管理</h2>
    <!-- <el-button type="success" size="small" round @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small" round @click="toDeleteAllHandler">批量删除</el-button> -->

    <!-- {{ orders.list }}
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
    <el-table :data="orders" stripe>
      <!-- <el-table-column type="selection" /> -->

      <el-table-column prop="id" label="编号" />
      <el-table-column prop="orderTime" label="订单日期" />
      <el-table-column prop="total" label="总价" />
      <el-table-column prop="status" label="状态" />
      <el-table-column prop="customerId" label="顾客id" />
      <el-table-column v-slot="slot" label="操作">
        <template>
          <a href="" @click.prevent="toShowHandler(slot.row)"> <i class="el-icon-info" /></a>
          <a v-if="slot.row.status === '待派单'" href="" @click.prevent="toSendHandler(slot.row)"> <i class="el-icon-edit" /></a>

        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      layout="prev, pager, next"
      :total="orders.total"
      @current-change="pageChangeHandler"
    />

    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%"
    >

      编号 :  {{ shown.id }} <br>
      下单时间 :  {{ shown.orderTime }} <br>
      总价 :  {{ shown.total }} <br>
      状态 :  {{ shown.status }} <br>
      标记 :  {{ shown.remark }} <br>
      顾客id :  {{ shown.customerId }} <br>
      员工id :  {{ shown.waiterId }} <br>
      地址id :  {{ shown.addressId }} <br>

      <span slot="footer" class="dialog-footer">
        <!-- <el-button size="small" @click="closeModalHandler">取 消</el-button> -->
        <el-button type="primary" size="small" @click="closeModalHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 提示框 :before-close="handleClose"-->
    <el-dialog
      :title="title"
      :visible.sync="visibleToSend"
      width="60%"
    >

      <div>
        <p>订单编号： {{ shown.id }}</p>
        <p>订单总价： {{ shown.total }}</p>
        <p>下单时间： {{ shown.orderTime }}</p>
        <p>下单用户编号： {{ shown.customerId }}</p>
        <!-- {{ shown }} -->
        指定派单人员: <br>
        <template>
          <el-radio-group v-for="ele in employees" :key="ele.key" v-model="employees.id" @change="toBuildOrderHandler(ele.id)">
            <el-radio :label="ele.realname" style="padding:1em" />
          </el-radio-group>

        </template></div>
      <span slot="footer" class="dialog-footer">
        <!-- <el-button size="small" @click="closeModalHandler">取 消</el-button> -->
        <el-button type="primary" size="small" @click="toSendOrderHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!--   {{ form }}
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
import querystring from 'querystring'
export default {
  // data用于存放要向网页中存放的数据
  data() {
    return {
      employees: [],
      activeName: 'first',
      visible: false,
      visibleToSend: false,
      title: '',
      form: {
      },
      //   options: [],
      orders: [],
      shown: [
      ],
      params: {
        page: 0,
        pageSize: 10,
        status: null
      },
      sendUrl: ''
    }
  }, created() {
    console.log('Created')
    this.loadData()
  },
  // 存放网页中需要调用的方法
  methods: {
    toBuildOrderHandler(builder) {
      console.log('............订单中派单员信息' + builder + this.shown.id)
      this.sendUrl = 'http://localhost:6677/order/sendOrder?waiterId=' + builder + '&orderId=' + this.shown.id
    },
    toSendOrderHandler() {
      console.log('')
      request.get(this.sendUrl).then((response) => {
        this.$message({
          type: 'success',
          message: response.message
        })
      })
      this.visibleToSend = false
    },
    toSendHandler(row) {
      this.title = '派送订单'
      this.visibleToSend = true
      const url = 'http://localhost:6677/waiter/findAll'
      request.get(url).then((response) => {
        this.employees = response.data
      })
      this.shown = row
      this.employees.forEach(element => {
        if (row.customerId === element.id) {
          this.shown.customerName = element.name
        }
      })
    },
    handleClick() {
      switch (this.activeName) {
        case 'first':
          this.params.status = null
          this.loadData()
          console.log('InSwitch')
          break
        case 'second':
          this.params.status = '待支付'

          this.loadData()
          console.log('InSwitch')
          break
        case 'third':
          this.params.status = '待派单'
          console.log('InSwitch')
          this.loadData()
          break
        case 'fourth':
          this.params.status = '待接单'
          console.log('InSwitch')
          this.loadData()
          break
        case 'fifth':
          this.params.status = '待服务'
          console.log('InSwitch')
          this.loadData()
          break
        case 'sixth':
          this.params.status = '待确认'
          console.log('InSwitch')
          this.loadData()
          break
        case 'seventh':
          this.params.status = '已完成'
          console.log('InSwitch')
          this.loadData()
          break
      }
    },
    loadData() {
      const url = 'http://localhost:6677/order/queryPage'
      request({
        url,
        method: 'post',
        headers: {
          'Content-type': 'application/x-www-form-urlencoded'
        },
        data: querystring.stringify(this.params)
      }).then((response) => {
        this.orders = {}
        this.orders = response.data.list
      })
    },
    // loadNav(statu) {
    //   if (statu === '') {
    //     this.loadData()
    //   }
    //   this.showList = []
    //   this.orders.list.forEach((toShow) => {
    //     if (toShow.status === statu) {
    //       this.showList.push(toShow)
    //     }
    //   })
    // },
    pageChangeHandler(page) {
      this.params.page = page - 1
      this.loadData()
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
    },
    // toUpdateHandler(row) {
    //   this.form = row
    //   this.visible = true
    // }
    // ,
    toShowHandler(row) {
      this.shown = row
      this.title = '订单详情'
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
