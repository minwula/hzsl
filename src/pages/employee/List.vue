<template>
  <div>
    <!-- 按钮 -->
    <el-button round type="success" icon="el-icon-edit" size="small" @click="toAddHandler"> 添加 </el-button> 
    <el-button round type="danger" icon="el-icon-delete" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="employee">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="realname" label="真实姓名"></el-table-column>
      <el-table-column prop="username" label="用户名"></el-table-column>
      <el-table-column prop="telephone" label="联系方式"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
		
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)"> <i class="el-icon-delete"></i></a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)"> <i class="el-icon-edit"></i></a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      title="录入员工信息"
      :visible.sync="visible"
      width="60%">
      <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.passworm"></el-input>
        </el-form-item>
           <el-form-item label="真实姓名">
          <el-input v-model="form.realname"></el-input>
        </el-form-item>
          <el-form-item label="联系方式">
          <el-input v-model="form.telephone"></el-input>
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
import request from'@/utils/request'
import querystring from 'querystring'
export default 
{
  // 用于存放网页中需要调用的方法
  methods:{
    loadData(){
 let url="http://localhost:6677/employee/findAll"
    request.get(url).then((response)=>{
      //将查询结果设置到customer中
      this.employee = response.data;
    })
    },
    submitHandler(){
      //this.form对象---字符串--》后台
      //通过request与后台进行交互,需携带参数
      let url="http://localhost:6677/employee/saveOrUpdate"
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)

      }).then((response)=>{
        this.closeModalHandler();
        // 模态框关闭
        //刷新
        this.loadData();
        //提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //调用后台接口，完成删除信息
        let url = "http://localhost:6677/employee/deleteById?id="+id;
        request.get(url).then((response)=>{
          //刷新数据
            this.loadData();
          //提示结果
            this.$message({
            type: 'success',
            message:response.message
          });
        })
        
      })
      
    },
    toUpdateHandler(row){
      this.form = row;
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },

    toAddHandler(){
      this.form ={
        type:"employee"
      }
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      customer:[],
      form:{
        type:"employee"
      }
    }
  },
  created(){
    //this为当前实例
    //vue实例创建完毕
   this.loadData();
  }
}
</script>
<style scoped>
 
</style>