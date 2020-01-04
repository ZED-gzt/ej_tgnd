<template>
    <div>
        顾客管理
    <br/>
    <br/>
   <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
   <el-button type="danger" size="small">批量删除</el-button>
   <el-table :data="customers">
       <el-table-column prop="id" label="编号"></el-table-column>
       <el-table-column prop="username" label="姓名"></el-table-column>
        <el-table-column prop="realname" label="真实姓名"></el-table-column>
       <el-table-column prop="telephone" label="联系方式"></el-table-column>
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
       title="修改顾客信息"
       :visible.sync="visible"
       width="60%"
       >
       ---{{form}}
       <el-form :model="form" label-width="80px">
           <el-form-item label="用户名">
               <el-input v-model="form.username"></el-input>
           </el-form-item>
            <el-form-item label="密码">
               <el-input v-model="form.password" type="password"></el-input>
           </el-form-item>
           <el-form-item label="真实姓名">
               <el-input v-model="form.realname" ></el-input>
           </el-form-item>
           <el-form-item label="手机号">
               <el-input v-model="form.telephone" ></el-input>
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
           let url = "http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.customers = response.data;
        })
        },
        submitHandler(){
            //通过request与后台进行交互，并且要携带参数
            let url = "http://localhost:6677/customer/saveOrUpdate"
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
                let url = "http://localhost:6677/customer/deleteById?id="+id;
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
                type:"costomer"
            }
            this.visible = true;
        }

    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            customers:[],
            form:{
                type:"customer"
            }
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
