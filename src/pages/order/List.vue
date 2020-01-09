<template>

    <div>
       
    <template>
  <el-tabs v-model="params.status" @tab-click="loadData"> 
    <el-tab-pane label="全部" name="全部"></el-tab-pane>
    <el-tab-pane label="待派单" name="待派单"></el-tab-pane>
    <el-tab-pane label="待接单" name="待接单"></el-tab-pane>
    <el-tab-pane label="待服务" name="待服务"></el-tab-pane>
     <el-tab-pane label="待确认" name="待确认"></el-tab-pane>
      <el-tab-pane label="已完成" name="已完成"></el-tab-pane>
  </el-tabs>
</template>
        
        <el-table :data="order.list">
            <el-table-column label="订单编号" prop="id"> </el-table-column>
            <el-table-column label="下单时间" prop="orderTime"> </el-table-column>
            <el-table-column label="总价" prop="total"> </el-table-column>
            <el-table-column label="状态" prop="status"> </el-table-column>
            <el-table-column label="顾客id" prop="customerId"> </el-table-column>
             <el-table-column label="服务地址id" prop="addressId"> </el-table-column>
             <el-table-column label="员工id" prop="waiterId"> </el-table-column>
            <el-table-column fixed="right" label="操作" > 
                <template v-slot="slot">
                    <a href="javascript:void(0)" >详情</a>
                    <a href=""  v-if="slot.row.status==='待派单'" @click.prevent="toSendOrderandler (slot.row)" >派单</a>
                </template>
            </el-table-column>
            
        </el-table>
        <!--/表格-->
        <!--分页-->
         <el-pagination
         layout="prev, pager, next"
        :total="order.total"
         @current-change="pageChageHandler">
       </el-pagination>
       <!--/分页-->
        <!--模式框-->
       <el-dialog
         title="派单"
         :visible.sync="visible"
         width="60%">
         <div>
             <p><strong>订单编号：</strong>{{form.id}}</p>
             <p><strong>订单总价：</strong>{{form.total}}</p>
             <p><strong>下单时间：</strong>{{form.orderTime}}</p>
             <p><strong>服务员工：</strong>
             <el-radio-group v-model="waiterId">
                <el-radio v-for="e in employees"
                         :key="e.id"
                         :label="e.id"
                         border
                         size="small">{{e.realname}}</el-radio>
                
            </el-radio-group></p>
         </div>
        
         
         <span slot="footer" class="dialog-footer">
         <el-button @click="closeModalHander" size="small">取 消</el-button>
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
        <!--/模式框-->
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from'querystring'
export default {
    //用于存放网页中用于调用的方法
    methods:{
        pageChageHandler(page){
        // 将params中当前页改为插件中的当前页
        this.params.page = page-1;
        // 加载
        this.loadData();
    },
        
           loadData(){
      let url = "http://localhost:6677/order/queryPage"
      if(this.params.status==="全部"){
          delete this.params.status;
      }
      request({
          url,
          method:"post",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data:querystring.stringify(this.params)
      }).then((response)=>{
          // orders为一个对象，其中包含了分页信息，以及列表信息
          this.order = response.data;
      })
    },
        submitHandler(){

            let url = "http://localhost:6677/order/sendOrder"
            request({
                url,
                method:"GET",
                params:{
                    orderId:this.form.id,
                    waiterId:this.waiterId
                }
            }).then((response)=>{
                this.closeModalHander();
                this.$message({
                    type:"success",
                    message:response.message
                
                });
                this.visible=false;
                 this.loadData();
            })
                    },
        //去派单
        toSendOrderandler(row){
            this.form = row;
            this.visible=true;
          
        },
        toAddHandler(){
            
            this.form = {
                type:"order"
            };
           
            this.visible=true;
        },
        closeModalHander(){
            this.visible=false;
        },
        //加载员工信息
        loadEmployees(){
            let url="http://localhost:6677/waiter/findAll";
            request.get(url).then(response=>{
                this.employees=response.data;
            })
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            order:{},
           form:{},//当前订单信息
      params:{
          page:0,
          pageSize:10,
      },
      employees:[],
      waiterId:null//选中的员工
    }
  },
    created(){
        // 文档加载完毕/vue实例创建完毕
        // this为当前Vue实例
            this.loadData();
            this.loadEmployees();
    }
}
</script>

<style scoped>