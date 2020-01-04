<template>
  <div>
    <h2>产品管理</h2>
    <el-button type="success" size="small" round @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small" round @click="toDeleteAllHandler">批量删除</el-button>

    <el-table :data="products">
      <el-table-column prop="id" label="编号" />
      <el-table-column prop="name" label="产品名称" />
      <el-table-column prop="price" label="价格" />
      <el-table-column prop="description" label="描述" />
      <el-table-column prop="categoryId" label="所属产品" />
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">编辑</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      layout="prev, pager, next"
      :total="50"
    />

    <!-- 提示框 :before-close="handleClose"-->
    <el-dialog
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
          <el-input v-model="form.price" type="password" />
        </el-form-item>
        <el-form-item label="所属栏目">
          <el-select v-model="value" placeholder="请选择">
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
    </el-dialog>

  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // data用于存放要向网页中存放的数据
  data() {
    return {
      visible: false,
      title: '',
      form: {
        type: 'product'
      },
      options: [],
      products: [
        // 以下为模拟数据
        // {
        //   id: 1,
        //   name: '候泽空',
        //   gender: '男',
        //   tel: '55522266333'
        // }, {
        //   id: 2,
        //   name: '蜘蛛侠',
        //   gender: '男',
        //   tel: '99844566123'
        // }
      ]
    }
  }, created() {
    this.loadData()
  },
  // 存放网页中需要调用的方法
  methods: {
    loadData() {
      const url = 'http://localhost:6677/product/findAll'
      request.get(url).then((response) => {
        this.products = response.data
      })
    },
    toAddHandler() {
      const url = 'http://localhost:6677/category/findAll'
      request.get(url).then((response) => {
        this.options = response.data
      })
      this.title = '添加产品信息'
      this.visible = true
    },
    closeModalHandler() {
      this.visible = false
    }, toUpdateHandler() {
      this.visible = true
    }, toDeleteHandler(id) {
      this.$confirm('此操作将永久删除该行, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const url = 'http://localhost:6677/product/deleteById?id=' + id
        request.get(url).then((response) => {
          this.loadData()
        })
        this.$message({
          type: 'success',
          message: '第 ' + id + ' 行删除成功!'
        })
      })
    }, toDeleteAllHandler() {
      this.title = '删除产品信息'
      this.visible = true
    }, submitHandler() {
      const url = 'http://localhost:6677/product/saveOrUpdate'
      request({
        url,
        method: 'post',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        data: querystring.stringify(this.form)
      }).then((response) => {
        this.loadData()
        this.$message({
          type: 'success',
          message: response.message
        })
        this.closeModalHandler()
      })
    }
  }
}
</script>

<style scoped>

</style>
