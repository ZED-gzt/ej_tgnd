<template>
    <div>
        栏目管理
    <br/>
    <br/>
   <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
   <el-button type="danger" size="small">批量删除</el-button>
   <el-table :data="columns">
       <el-table-column prop="id" label="编号"></el-table-column>
       <el-table-column prop="name" label="栏目名称"></el-table-column>
        <el-table-column prop="num" label="序号"></el-table-column>
       <el-table-column prop="parentId" label="父栏目"></el-table-column>
       <el-table-column label="操作">
           <template v-slot="slot"> 
               <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
               <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
           </template>
       </el-table-column>
   </el-table>
    <!-- <el-pagination
    layout="prev, pager, next"
    :total="1000">
    </el-pagination> -->
    <el-dialog
       title="修改栏目信息"
       :visible.sync="visible"
       width="60%"
       >
       <el-form :model="form" label-width="80px">
           <el-form-item label="栏目名称">
               <el-input v-model="form.name"></el-input>
           </el-form-item>
            <el-form-item label="序号">
               <el-input v-model="form.num"></el-input>
           </el-form-item>
           <el-form-item label="父栏目">
           <template>
  <el-select v-model="form.parentId" placeholder="请选择">
    <el-option
      v-for="item in options"
      :key="item.id"
      :label="item.name"
      :value="item.id">
    </el-option>
  </el-select>
</template>
           </el-form-item>
       </el-form>
  <span></span>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler" size="small">取 消</el-button>
    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
  </span>
  </el-dialog>
    </div>
</template>


<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
           let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到columns中,this指向外部函数的this
            this.columns = response.data;
            this.options=response.data;
        })
        },
        submitHandler(){
            //通过request与后台进行交互，并且要携带参数
            let url = "http://localhost:6677/category/saveOrUpdate"
            request({
                    url,
                    method:"POST",
                    headers:{
                        "Content-Type":"application/x-www-form-urlencoded"
                    },
                    data:querystring.stringify(this.form)
                }).then((response)=>{
                    //请求结束
                    this.closeModalHandler();
                    //模态框关闭
                    this.loadData();
                    this.$message({
                    //提示消息
                        type: "success",
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
                let url = "http://localhost:6677/category/deleteById?id=" + id;
                request.get(url).then((response)=>{
                    //刷新数据
                    this.loadData();
                    //提示结果
                    this.$message({
                    type: 'success',
                    message: response.message
                });
                })
            })
        },

        toUpdataHandler(row){
            this.form=row;
            this.visible = true;
        
        },

        closeModalHandler(){
            this.visible = false;
        },

        toAddHandler(){
            this.form={
                type:"column"
            }
            this.visible = true;
        }

    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            columns:[],
            form:{
            },
            options:[],
        }
    },
    created(){
        //Vue实例创建完毕
        this.loadData()
    }
    
}
</script>

<style scoped>
 
</style>
