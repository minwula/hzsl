<template>
  <div>
    <h2>产品管理</h2>
    <el-button type="success" size="small" round @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small" round @click="toDeleteAllHandler">批量删除</el-button>
    <el-table :data="products" stripe @selection-change="handleSelectionChange">
      <el-table-column type="selection" />

      <el-table-column prop="id" label="编号" />
      <el-table-column prop="name" label="产品名称" />
      <el-table-column prop="price" label="价格" />
      <el-table-column label="照片">
        <el-image
          style="width: 100px; height: 100px"
          :src="products.photo"
          fit="fill"
        >
          <div slot="error" class="image-slot">
            <i class="el-icon-picture-outline" />
          </div>
        </el-image>
      </el-table-column>
      <el-table-column prop="description" label="描述" />
      <el-table-column prop="categoryId" label="所属产品" />

      <el-table-column v-slot="slot" fixed="right" label="操作">
        <template>
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)"> <i class="el-icon-delete" /></a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)"> <i class="el-icon-edit" /></a>
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
          <el-input v-model="form.price" />
        </el-form-item>
        <el-form-item label="所属栏目">
          <el-select v-model="form.num" placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.id"
              :label="item.name"
              :value="item.name"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="介绍">
          <el-input v-model="form.description" />
        </el-form-item>
        <el-form-item label="产品主图">
          <!-- :on-preview="handlePreview"
            :on-remove="handleRemove" -->
          <el-upload
            class="upload-demo"
            action="https://134.175.154.93:6677/file/upload"
            :on-success="onsuccessUpLoadHandler"
            :file-list="fileList"
            list-type="picture"
          >
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
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
      ],
      delIds: [],
      delarr: [],
      fileList: [
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
    onsuccessUpLoadHandler(response) {
      const photo = 'https://134.175.154.93:8888/group1/'
      console.log('上传图片返回信息' + response)
      this.form.photo = photo
    },
    toggleSelection(rows) {
      if (rows) {
        rows.forEach(row => {
          this.$refs.multipleTable.toggleRowSelection(row)
        })
      } else {
        this.$refs.multipleTable.clearSelection()
      }
    },
    // 多选删除！！！！！！！！！！！！！！！！！
    handleSelectionChange(val) {
      this.delIds = val
    },
    toAddHandler() {
      this.fileList = []
      this.form = {
      }
      const url = 'http://localhost:6677/category/findAll'
      request.get(url).then((response) => {
        this.options = response.data
      })
      this.title = '添加产品信息'
      this.visible = true
    },
    closeModalHandler() {
      this.visible = false
    }, toUpdateHandler(row) {
      this.form = row
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
      // 删除所有选中项！！
      this.delIds.forEach((object) => {
        this.delarr.push(object.id)
      })
      const url = 'http://localhost:6677/product/batchDelete?ids=' + this.delarr
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
      })
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
    }, handleRemove(file, fileList) {
      console.log(file, fileList)
    },
    handlePreview(file) {
      console.log(file)
    }
  }
}
</script>

<style scoped>

</style>
