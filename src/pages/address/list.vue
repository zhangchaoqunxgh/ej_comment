<template>
  <div>   
    <!-- 按钮 -->
  <div>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
  </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="addresss" style="width: 100%" :row-class-name="tableRowClassName">
        <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
        <el-table-column fixed="left" prop="province" label="省份"></el-table-column>
        <el-table-column prop="city" label="城市"></el-table-column>
        <el-table-column prop="area" label="地区"></el-table-column>
        <el-table-column width="120" prop="address" label="具体地址"></el-table-column>
        <el-table-column width="200" prop="telephone" label="手机号"></el-table-column>
        <el-table-column width="200" prop="customerId" label="顾客编号"></el-table-column>
        <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
           <a href="" @click.prevent="toDeleteHandler(slot.row.id)" >删除</a>
          <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
    <!-- 显示脚本数据 -->
    <!-- {{slot}} -->
  </template>
</el-table-column>
        </el-table>
    <!-- /表格 -->
    <!-- 分页 -->
      <!--分页开始-->
  <!-- <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination> -->
<!--/分页结束-->
    <!-- /分页 -->
    <!-- 模态框 -->
    <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
  <el-form label-width="80px">
    <el-form-item label="编号">
      <el-input v-model="form.id"/>
    </el-form-item>
    <el-form-item label="省份">
      <el-input v-model="form.province"/>
    </el-form-item>
    <el-form-item label="地区">
      <el-input v-model="form.area"/>
    </el-form-item>
    <el-form-item label="具体地址">
      <el-input v-model="form.address"/>
    </el-form-item>
    <el-form-item label="手机号">
      <el-input v-model="form.telephone"/>
    </el-form-item>
    <el-form-item label="顾客编号">
      <el-input v-model="form.customerId"/>
    </el-form-item>

  </el-form>
  
  <span slot="footer" class="dialog-footer">
    <el-button size="small" type="success" @click="closeModelHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    <!-- /模态框 -->



    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    
    methods:{
      loadData(){
              // vue文档创建完毕所执行操作
      let url="http://localhost:6677/address/findAll"
      request.get(url).then((response)=>{
        //箭头函数中的this指向外部函数中的this
        // 将查询结果设置到customers中，this指向外部函数的this
        this.addresss = response.data;
        
      })
      },
      submitHandler(){
     //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
        let url="http://localhost:6677/address/saveOrUpdate"
        //前端向后台发送请求，保存数据的保存操作
        request({
            url,
          method:"POST",
           header:{
           "content-Type":"application/x-www-form-urlencoded"
          },
       //数据回调 then 返回结果
      data:querystring.stringify(this.form)
        }).then((response)=>{
          //请求结束
          this.closeModelHandler();
                  // 刷新
        this.loadData();
        // 提示消息
          this.$message({
          type:"success",
          message:response.message
        })
        })

      },
            toUpdataHandler(row){
              this.title="修改地址信息";
               this.form=row;
this.visible = true;

      },
       toDeleteHandler(id){
            //确认
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
            let url="http://localhost:6677/address/deleteById"
            request.get(url+"?id="+id).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                this.$message({
                type: 'success',
                message: '删除成功!'+id//测试id是否拿到
            });
            })
        })
        },
      toAddHandler(){
        this.title="录入地址信息";
          //将form变为初始值
            this.form = {
                type:"address"
            }
        this.visible = true;
      },
      closeModelHandler(){
         this.visible = false;
      }
    },
    data(){
      return {
        visible:false,
        addresss:[],
        form:{
          type:"address"
        }
      }
    },
    created(){
    this.loadData();
    }
}
</script>
<style scoped>

</style>>