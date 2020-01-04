<template>
 <div>
   地址管理
   <!-- 按钮 -->
   <el-button type="warning" @click="toAddHandler">添加</el-button>
   <!-- 表格 -->
   <el-table :data="adresses">
     <el-table-column label="省份" prop="province"></el-table-column>
     <el-table-column label="市区" prop="city"></el-table-column>
     <el-table-column label="其他" prop="area"></el-table-column>
     <el-table-column label="地址" prop="adress"></el-table-column>
     <el-table-column label="电话" prop="telephone"></el-table-column>
     <el-table-column label="用户id" prop="customerId"></el-table-column>
     <el-table-column label="操作">
         <template v-slot="slot">
           <!-- 操作 -->
       <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
       <a href="" @click.prevent="toUppdataHandler(slot.row)">修改</a>
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
 <el-form :model="form" label-width="80px">
   <el-form-item label="省份">
     <el-input v-model="form.province"></el-input>
   </el-form-item>
   <el-form-item label="市区">
     <el-input v-model="form.city"></el-input>
   </el-form-item>
    <el-form-item label="地址">
     <el-input v-model="form.adress"></el-input>
   </el-form-item>
   <el-form-item label="其他">
     <el-input v-model="form.area"></el-input>
   </el-form-item>
   <el-form-item label="电话">
     <el-input v-model="form.telephone"></el-input>
   </el-form-item>
 </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
 </div>
</template>
<script>