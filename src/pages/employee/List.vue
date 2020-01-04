<template>
  <div>
      员工管理
   <!-- 按钮 -->
   <el-button type="warning" @click="toAddHandler" size="small">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
   <!-- 表格 -->
   <el-table :data="employees">
     <el-table-column fix = "left" label="密码" prop="password"></el-table-column>
     <el-table-column fix = "left" label="姓名" prop="username"></el-table-column>
     <el-table-column fix = "left" label="真实姓名" prop="realname"></el-table-column>
     <el-table-column label="性别" prop="gender"></el-table-column>
     <el-table-column width = 150 label="电话号码" prop="telephone"></el-table-column>
     <el-table-column width = 200 label="身份证号" prop="idCard"></el-table-column>
     <el-table-column width = 200 label="银行卡号" prop="bankCard"></el-table-column>
     <el-table-column label="操作">
     <!-- slot获取当前行数据 -->
     <template v-slot="slot">
       <!-- 操作 -->
       <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
       <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
     </template>
         </el-table-column>
   </el-table>
   <!-- 分页 -->
   <el-pagination
    layout="prev, pager, next"
    :total="50">
   </el-pagination>
   <!-- 点击添加按钮，弹窗 -->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%"
  >
  测试：{{form}}
    <el-form label-width="80px">
           <el-form-item label="用户名">
               <el-input v-model="form.username"></el-input>
           </el-form-item>
            <el-form-item label="密码">
               <el-input v-model="form.password" type="password"></el-input>
           </el-form-item>
           <el-form-item label="性别">
                <el-radio-group v-model="form.gender">
                  <el-radio label="男">男</el-radio>
                  <el-radio label="女">女</el-radio>
                </el-radio-group>
           </el-form-item>
           <el-form-item label="真实姓名">
               <el-input v-model="form.realname" ></el-input>
           </el-form-item>
           <el-form-item label="手机号">
               <el-input v-model="form.telephone" ></el-input>
           </el-form-item>
           <el-form-item label="银行卡号">
               <el-input v-model="form.bankCard" ></el-input>
           </el-form-item>
           <el-form-item label="身份证号">
               <el-input v-model="form.idCard" ></el-input>
           </el-form-item>
       </el-form>
  <span></span>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  created(){
    this.loadData();
  },
  data(){
        return{
       visible:false,
       title:"录入信息",
       employees:[],
        form:{
          type:"waiter"
        }
      }
    },
 methods:{
   submitHandler(){
     this.visible=false;
     let url = "http://localhost:6677/waiter/saveOrUpdate";
     request({
       url,
       method:"post",
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
   loadData(){
     let url = "http://localhost:6677/waiter/findAll";
     request.get(url).then((response)=>{

       
       //箭头函数中的this指向外部函数中的this
       this.employees = response.data;
     })
   },
      toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let url = "http://localhost:6677//waiter/deleteById?id="+id;
            request.get(url).then((response)=>{
                    //刷新数据
                    this.loadData();
                    //提示结果
                    this.$message({
                    type: 'success',
                    message: response.message
                });
          });
        })
      },
        toAddHandler(){
          this.form={
            type:"employee"
          },
        this.visible=true;
        this.title="录入员工信息";
           },
        closeModalHandler(){
       this.visible=false;
        },
        toUpdateHandler(row){
           this.form=row;
           this.visible=true; 
           this.title="修改员工信息";
        }
 },
 
}
</script>

<style scoped>

</style>
