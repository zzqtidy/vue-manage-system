<template>
    <div>
        <el-table :data="tableData" style="width: 100%" :row-class-name="tableRowClassName">
            <el-table-column label="操作" width="100">
                <template slot-scope="scope">
                    <i class="el-icon-edit table-icon" @click="handleEdit(scope.$index, scope.row)"></i>
                </template>
            </el-table-column>
            <el-table-column prop="id" label="ID" width="80"></el-table-column>
            <el-table-column prop="username" label="用户编码"></el-table-column>
            <el-table-column prop="name" label="用户名称"></el-table-column>
            <el-table-column prop="telephone" label="固话"></el-table-column>
            <el-table-column prop="phone" label="手机"></el-table-column>
            <el-table-column prop="address" label="地址"></el-table-column>
            <el-table-column prop="enabled" label="激活状态" v-if=false></el-table-column> <!--隐藏-->
            <el-table-column label="激活状态" width="">
                <template scope="scope">
                    {{scope.row.enabled==='0'?'禁用':'启动'}}
                </template>
            </el-table-column>
        </el-table>
        <el-row>
            <el-button type="primary" @click="getData">获取数据</el-button>
        </el-row>

        <!--编辑界面弹出框-->
        <el-dialog title="用户信息" :visible.sync="dialogFormVisible" width="30%">
            <el-form  :model="tableRowData" label-width="100px">

                <el-form-item label="用户编码">
                    <el-input v-model="tableRowData.username"></el-input>
                </el-form-item>
                <el-form-item label="用户名称">
                    <el-input v-model="tableRowData.name"></el-input>
                </el-form-item>
                <el-form-item label="固话">
                    <el-input v-model="tableRowData.telephone"></el-input>
                </el-form-item>
                <el-form-item label="手机">
                    <el-input v-model="tableRowData.phone"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="tableRowData.address"></el-input>
                </el-form-item>
                <el-form-item label="激活状态">
                    <el-select v-model="tableRowData.enabled" placeholder="请选择" style="width: 100%">
                        <el-option key="0" label="禁用" value="0"></el-option>
                        <el-option key="1" label="启动" value="1"></el-option>
                    </el-select>
                </el-form-item>

            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="handleEditSave()">确 定</el-button>
            </div>
        </el-dialog>
    </div>

</template>

<style>
    .el-table .warning-row {
        background: oldlace;
    }

    .el-table .success-row {
        background: #F0F0F0;
    }

    .table-icon {
        padding: 0 10px;
        cursor: pointer;
        color: #242f42;
    }
</style>

<script>
    export default {
        name: "TestTable",
        data() {
            return {
                tableData: [],
                url: "http://localhost:8081/sys_user/all",
                dialogFormVisible: false,
                tableRowData: {}
            }
        },
        methods: {
            getData() {
                // console.log("getData中this:");
                // console.log(this);
                this.$axios({
                    method: "post",
                    url: this.url,
                    headers: {
                        Authorization: localStorage.getItem('ms_user_token')
                    }
                }).then((res) => {
                    this.tableData = res.data.list;
                }).catch((res) => {
                    console.log(res);
                })
            },
            tableRowClassName({row, rowIndex}) {
                if (rowIndex % 2 === 0) {
                    return 'warning-row';
                } else {
                    return 'success-row';
                }
                return '';
            },
            handleEdit(index, row) {
                this.dialogFormVisible = true;
                this.tableRowData = row;
            },
            //编辑保存
            handleEditSave(){
                this.$axios({
                    method: "post",
                    url: "http://localhost:8081/sys_user/update",
                    data:this.tableRowData,
                    headers: {
                        Authorization: localStorage.getItem('ms_user_token')
                    }
                }).then((res) => {
                    if(res.data.meta.success){
                        this.dialogFormVisible = false;
                    }
                    else
                    {
                        alert("更新失败");
                    }
                }).catch((res) => {
                    console.log(res);
                    alert("更新异常");
                })
            }
        },
        created() {
            //一个实例被创建之后执行,注意，这个生命周期钩子方法如果要调用method中方法，需要将method放于该方法之前
            this.getData();
        },
        computed: {
        }
    }
</script>

