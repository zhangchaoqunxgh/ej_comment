<template>
    <div>
<!--按钮-->
<div>
    <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
    <el-button size="small" type="danger">批量删除</el-button>
</div>
<!--/按钮-->
<!--表格-->
<el-table :data="employees">
    <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
    <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
    <el-table-column prop="realname" label="姓名"></el-table-column>
    <el-table-column prop="gender" label="性别"></el-table-column>
    <el-table-column prop="telephone" width="120" label="手机号"></el-table-column>
    <el-table-column prop="idCard" width="200" label="身份证号"></el-table-column>
    <el-table-column prop="bankCard" width="200" label="银行卡号"></el-table-column>
    <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
        <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
        <a href="" @click.prevent="toUpdateHandler">修改</a>
        </template>
    </el-table-column>
</el-table>
<!--/表格-->
<!--分页-->
<el-pagination layout="prev, pager, next" :total="50"></el-pagination>
<!--/分页-->
<!--模态框-->
 <el-dialog
      title="录入员工信息"
      :visible.sync="visible"
      width="60%">
      测试：{{form}}
      <el-form :model="form" label-width="80px">
          <el-form-item label="用户名">
              <el-input v-model="form.username"/>
          </el-form-item>
           <el-form-item label="密码">
              <el-input type="password" v-model="form.password"/>
          </el-form-item>
           <el-form-item label="姓名">
               <el-input v-model="form.realname"/>
          </el-form-item>
           <el-form-item label="性别">
               <el-radio-group v-model="form.gender">
            <el-radio label="男">男</el-radio>
            <el-radio label="女">女</el-radio>
             </el-radio-group>
              <el-input/>
          </el-form-item>
           <el-form-item label="手机号">
              <el-input v-model="form.telephone"/>
          </el-form-item>
           <el-form-item label="身份证号">
              <el-input v-model="form.idCard"/>
          </el-form-item>
           <el-form-item label="银行卡号">
              <el-input v-model="form.bankCard"/>
          </el-form-item>
      </el-form>


      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
<!--/模态框-->

    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        this.loadDate()
    },
    methods:{
        submitHandler(){
            let url="http://localhost:6677/waiter/daveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,method:"post",
                //告诉给后台我的请求体中放的查询字符串
                headers:{
                    "content-type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
      //请求结束，模态框关闭
      this.closeModalHandler();
      //自动刷新
      this.loadData();
      this.$message({
        type:"success",
        message:response.message
      })
            })
        },
        loadDate(){
            //this=>指向vue实例，通过vue实例访问该实例中数据
            let url="http://localhost:6677/waiter/findAll"
            request.get(url).then(response=>{
                this.employees=response.data;
            })
        },
        toDeleteHandler(id){
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
        toUpdateHandler(row){
          this.form=row;
            this.title="修改员工信息";
            this.visible=true;
        },
        toAddHandler(){
          this.form={type:"employee"}
            this.title="录入员工信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }
    }
}
</script>

<style scoped>

</style>

