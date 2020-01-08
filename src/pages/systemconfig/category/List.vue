<template>
  <div>
    <h2>栏目管理</h2>
    <el-button type="success" size="small" icon="el-icon-edit-outline" round @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small" icon="el-icon-delete" round>批量删除</el-button>
    <el-table :data="categorys" stripe>
      <el-table-column type="selection" />
      <el-table-column
        prop="id"
        label="编号"
      />
      <el-table-column
        prop="name"
        label="栏目名称"
      />
      <el-table-column
        prop="num"
        label="序号"
      />
      <el-table-column
        prop="parentId"
        label="父栏目"
      />
      <el-table-column
        label="操作"
      >
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete" /></a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)"><i class="el-icon-edit" /></a>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination layout="prev, pager, next" :total="50" />
    <!-- 模态框 -->
    <el-dialog
      title="录入栏目信息"
      :visible.sync="visible"
      width="60%"
    >
      <el-form :v-model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.name" />
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num" />
        </el-form-item>
        <el-form-item label="父栏目">
          <el-input v-model="form.parentId" />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeModalHandler">取消</el-button>
        <el-button type="primary" @click="submitHandler">确定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import request from '@/utils/request' // @代表src;
import querystring from 'querystring'
export default {
  data() {
    return {
      visible: false,
      categorys: [],
      form: {}
    }
  },
  created() {
    this.loadData()
  },
  methods: {
    loadData() {
      const url = 'http://localhost:6677/category/findAll'
      request.get(url).then((response) => {
        // 将查询结果设置到customers中，this指向外部函数的this
        this.categorys = response.data
      })
    },
    submitHandler() {
    // this.form 对象 ---字符串--->后台；
    // 通过request与后台进行交互,携带参数;
      const url = 'http://localhost:6677/category/saveOrUpdate'
      request({
        url,
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        data: querystring.stringify(this.form)

      }).then((response) => {
        // 模态框关闭
        this.closeModalHandler()
        this.loadData()
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
        const url = 'http://localhost:6677/category/deleteById?id=' + id
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
    closeModalHandler() {
      this.visible = false
    },
    toAddHandler() {
      this.form = {}
      this.visible = true
    },
    toUpdateHandler(row) {
      this.visible = true
      this.form = row
    }
  }
}
</script>
<style scoped>

</style>
