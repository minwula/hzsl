<template>
<<<<<<< HEAD
    <div>
        <h2>栏目管理</h2>
        <el-button type="primary" size="small" icon="el-icon-edit-outline" @click="toAddHandler" round>添加</el-button>
        <el-button type="danger" size="small" icon="el-icon-delete" round>批量删除</el-button>
        <el-table
            :data="categorys">
            <el-table-column
            width=30>
            <el-checkbox v-model="checked">
            </el-checkbox>
            </el-table-column>
            <el-table-column
            prop="id"
            label="编号"
            >
            </el-table-column>
            <el-table-column
            prop="name"
            label="栏目名称"
            >
            </el-table-column>
            <el-table-column
            prop="num"
            label="序号">
            </el-table-column>
             <el-table-column
            prop="parentId"
            label="父栏目">
            </el-table-column>
             <el-table-column
            label="操作">
                        <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete"></i></a>
                    <a href="" @click.prevent="toUpdateHandler"><i class="el-icon-edit"></i></a>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
         <el-pagination layout="prev, pager, next" :total="50">
         </el-pagination>
         <!-- 模态框 -->
         <el-dialog title="录入栏目信息" :visible.sync="visible"
          width="60%" >
             <el-form :v-model="form" label-width="80px">
                 <el-form-item label="编号">
                     <el-input v-model="form.id"></el-input>
                 </el-form-item>
                  <el-form-item label="栏目名称">
                     <el-input v-model="form.name"></el-input>
                 </el-form-item>
                 <el-form-item label="序号">
                     <el-input v-model="form.num"></el-input>
                 </el-form-item>
                 <el-form-item label="父栏目">
                     <el-input v-model="form.parentId"></el-input>
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
import request from '@/utils/request' //@代表src;
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
             let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response) =>{
            //将查询结果设置到customers中，this指向外部函数的this
            this.categorys = response.data;
        })
        },
        submitHandler(){
            //this.form 对象 ---字符串--->后台；
            //通过request与后台进行交互,携带参数;
            let url="http://localhost:6677/category/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form) 
            }).then((response) =>{
                //模态框关闭
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toDeleteHandler(id){
         //确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.visible=true;
        },
        toUpdateHandler(){
            this.visible=true;
        }
    },
    data(){
        return{
            visible:false, 
            categorys:[],
            form:{
                type: 'categorys'
            }
        }
    },
    created(){
        this.loadData();
    }
}
</script>
<style scoped>

</style>
=======
<div>
     <h2>栏目管理</h2>
    <el-button type="primary" size="small" icon="el-icon-edit-outline" round @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small" icon="el-icon-delete" round>批量删除</el-button>
     <el-table :data="categorys">
    <el-table-column type="selection">
    </el-table-column>
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
        <a href="" @click.prevent="toUpdateHandler"><i class="el-icon-edit" /></a>
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
        <el-form-item label="编号">
        <el-input v-model="form.id" />
        </el-form-item>
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
    form: {
        type: 'categorys'
    }
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
    // 确认
    this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
    }).then(() => {
        this.$message({
        type: 'success',
        message: '删除成功!'
        })
    })
    },
    closeModalHandler() {
    this.visible = false
    },
    toAddHandler() {
    this.visible = true
    },
    toUpdateHandler() {
    this.visible = true
    }
}
}
</script>
<style scoped>

</style>
>>>>>>> 356956a0a344fa2a360492bba676147eccf6b57f
