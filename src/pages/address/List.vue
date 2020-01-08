<template>
  <div>
    <h2>地址管理</h2>
    <!-- 按钮 -->
    <el-button round type="success" icon="el-icon-edit" size="small" @click="toAddHandler"> 添加 </el-button>
    <el-button round type="danger" icon="el-icon-delete" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table
      border
      :data="address"
      stripe
    >
      <el-table-column type="selection" />
      <el-table-column prop="id" label="用户编号" />
      <el-table-column prop="province" label="用户所在省" />
      <el-table-column prop="city" label="用户所在市" />
      <el-table-column prop="area" label="用户所在区" />
      <el-table-column prop="address" label="用户地址" />
      <el-table-column prop="telephone" label="用户联系方式" />
      <el-table-column prop="customerId" label="员工编号" />
      <el-table-column label="操作">
        <template v-slot="slot">
          <!-- slot 获取当前行信息 -->
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete" /></a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)"><i class="el-icon-edit" /></a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50" />
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      title="录入地址信息"
      :visible.sync="visible"
      width="50%"
    >
      <el-form :model="form" label-width="100px">
        <el-form-item label="用户编号" width="100px"><el-input v-model="form.id" /></el-form-item>
        <el-form-item label="用户所在省" width="100px"><el-input v-model="form.province" /></el-form-item>
        <el-form-item label="用户所在市" width="100px"><el-input v-model="form.city" /> </el-form-item>
        <el-form-item label="用户所在区" width="100px"><el-input v-model="form.area" /> </el-form-item>
        <el-form-item label="用户地址" width="100px"><el-input v-model="form.address" /> </el-form-item>
        <el-form-item label="用户联系方式" width="100px"><el-input v-model="form.telephone" /> </el-form-item>
        <el-form-item label="员工编号" width="100px"><el-input v-model="form.customerId" /> </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button round size="small" @click="closeModalHandler">取 消</el-button>
        <el-button round size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->

  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
// import { type } from 'os'
export default
{
  // 用于存放要向网页中显示的数据
  data() {
    return {
      visible: false,
      address: [],
      form: {
        type: 'address'
      }
    }
  },
  created() {
    // this为当前实例
    // vue实例创建完毕
    this.loadData()
  },
  // 用于存放网页中需要调用的方法
  methods: {
    loadData() {
      const url = 'http://localhost:6677/address/findAll'
      request.get(url).then((response) => {
      // 将查询结果设置到address中
        this.address = response.data
      })
    },
    submitHandler() {
      // this.form对象---字符串-->后台
      // 通过request与后台进行交互,需携带参数
      const url = 'http://localhost:6677/address/saveOrUpdate'
      request({
        url,
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        data: querystring.stringify(this.form)

      }).then((response) => {
        this.closeModalHandler()
        // 模态框关闭
        // 刷新
        this.loadData()
        // 提示消息
        this.$message({
          type: 'success',
          message: response.message
        })
      })
    },
    toDeleteHandler(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 调用后台接口，完成删除
        const url = 'http://localhost:6677/address/deleteById?id=' + id
        request.get(url).then((response) => {
          // 刷新数据
          this.loadData()
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
        })
      })
    },
    toUpdateHandler(row) {
      this.form = row
      this.visible = true
    },
    closeModalHandler() {
      this.visible = false
    },
    toAddHandler() {
      this.form = {
        type: 'address'
      }
      this.visible = true
    }
  }
}
</script>
<style scoped>

</style>
