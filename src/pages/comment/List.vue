<template>
  <div>
    <h2>评论管理</h2>
    <!-- 按钮 -->
    <el-button round type="success" icon="el-icon-edit" size="small" @click="toAddHandler"> 添加 </el-button>
    <el-button round type="danger" icon="el-icon-delete" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="comments">
      <el-table-column type="selection" />
      <el-table-column prop="id" label="编号" />
      <el-table-column prop="content" label="内容" />
      <el-table-column prop="commentTime" label="评论时间" />
      <el-table-column prop="orderId" label="订单号" />
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
      title="录入订单信息"
      :visible.sync="visible"
      width="60%"
    >
      <el-form :model="form" label-width="80px">
        <el-form-item label="内容">
          <el-input v-model="form.content" />
        </el-form-item>
        <el-form-item label="评论时间">
          <el-input v-model="form.commentTime" />
        </el-form-item>
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
      comments: [],
      form: {}
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
      const url = 'http://localhost:6677/comment/findAll'
      request.get(url).then((response) => {
      // 将查询结果设置到customer中
        this.comments = response.data
      })
    },
    submitHandler() {
      // this.form对象---字符串-->后台
      // 通过request与后台进行交互,需携带参数
      const url = 'http://localhost:6677/comment/saveOrUpdate'
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
        const url = 'http://localhost:6677/comment/deleteById?id=' + id
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
      this.form = {}
      this.visible = true
    }
  }
}
</script>
<style scoped>

</style>
