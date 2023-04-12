<template>
    <div style="padding: 10px">

        <!--    功能区域-->
        <div style="margin: 10px 0">
            <el-button type="primary" @click="add">新增</el-button>
            <el-upload
                :limit="1"
                :on-success="handleUploadSuccess"
                :show-file-list=false
                accept='.xlsx'
                action="http://localhost:9090/user/import"
                style="display: inline-block; margin: 0 10px"
            >
                <el-button type="primary">导入</el-button>
            </el-upload>
            <el-button type="primary" @click="exportUser">导出</el-button>
        </div>

        <!--    搜索区域-->
        <div style="margin: 10px 0">
            <el-input v-model="search" clearable placeholder="请输入关键字" style="width: 20%"></el-input>
            <el-button style="margin-left: 5px" type="primary" @click="load">查询</el-button>
        </div>
        <el-table
            v-loading="loading"
            :data="tableData"
            border
            stripe
            style="width: 100%">
            <el-table-column
                label="ID"
                prop="id"
                sortable
            >
            </el-table-column>
            <el-table-column
                label="用户名"
                prop="username">
            </el-table-column>
            <el-table-column
                label="昵称"
                prop="nickName">
            </el-table-column>
            <el-table-column
                label="年龄"
                prop="age">
            </el-table-column>
            <el-table-column
                label="性别"
                prop="sex">
            </el-table-column>
            <el-table-column
                label="地址"
                prop="address">
            </el-table-column>
            <el-table-column label="角色列表" width="300">
                <template #default="scope">
                    <el-select v-model="scope.row.roles" multiple placeholder="请选择" style="width: 80%">
                        <el-option v-for="item in roles" :key="item.id" :label="item.comment"
                                   :value="item.id"></el-option>
                    </el-select>
                </template>
            </el-table-column>
            <el-table-column label="操作" width="400">
                <template #default="scope">
                    <el-button size="mini" type="primary" @click="handleChange(scope.row)">保存角色信息</el-button>
                    <el-button plain size="mini" type="success" @click="showBooks(scope.row.bookList)">查看图书列表
                    </el-button>
                    <el-button plain size="mini" type="primary" @click="handleEdit(scope.row)">编辑</el-button>
                    <el-popconfirm title="确定删除吗？" @confirm="handleDelete(scope.row.id)">
                        <template #reference>
                            <el-button size="mini" type="danger">删除</el-button>
                        </template>
                    </el-popconfirm>
                </template>
            </el-table-column>
        </el-table>

        <div style="margin: 10px 0">
            <el-pagination
                :current-page="currentPage"
                :page-size="pageSize"
                :page-sizes="[5, 10, 20]"
                :total="total"
                layout="total, sizes, prev, pager, next, jumper"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange">
            </el-pagination>
        </div>


        <el-dialog v-model="bookVis" title="用户拥有的图书列表" width="30%">
            <el-table :data="bookList" border stripe>
                <el-table-column label="ID" prop="id"></el-table-column>
                <el-table-column label="名称" prop="name"></el-table-column>
                <el-table-column label="价格" prop="price"></el-table-column>
            </el-table>
        </el-dialog>

        <el-dialog v-model="dialogVisible" title="提示" width="30%">
            <el-form :model="form" label-width="120px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username" style="width: 80%"></el-input>
                </el-form-item>
                <el-form-item label="昵称">
                    <el-input v-model="form.nickName" style="width: 80%"></el-input>
                </el-form-item>
                <el-form-item label="年龄">
                    <el-input v-model="form.age" style="width: 80%"></el-input>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio v-model="form.sex" label="男">男</el-radio>
                    <el-radio v-model="form.sex" label="女">女</el-radio>
                    <el-radio v-model="form.sex" label="未知">未知</el-radio>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address" style="width: 80%" type="textarea"></el-input>
                </el-form-item>
                <el-form-item label="账户余额">
                    <el-input v-model="form.account" style="width: 80%"></el-input>
                </el-form-item>
            </el-form>
            <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="save">确 定</el-button>
          </span>
            </template>
        </el-dialog>

    </div>
</template>

<script>


import request from "@/utils/request";

export default {
    name: 'Home',
    components: {},
    data() {
        return {
            loading: true,
            form: {},
            dialogVisible: false,
            bookVis: false,
            search: '',
            currentPage: 1,
            pageSize: 10,
            total: 0,
            tableData: [],
            bookList: [],
            roles: []
        }
    },
    created() {
        this.load()
    },
    methods: {
        handleChange(row) {
            request.put("/user/changeRole", row).then(res => {
                if (res.code === '0') {
                    this.$message.success("更新成功")
                    if (res.data) {
                        this.$router.push("/login")
                    }
                }
            })
        },
        showBooks(books) {
            this.bookList = books
            this.bookVis = true
        },
        load() {
            this.loading = true
            request.get("/user", {
                params: {
                    pageNum: this.currentPage,
                    pageSize: this.pageSize,
                    search: this.search
                }
            }).then(res => {
                this.loading = false
                this.tableData = res.data.records
                this.total = res.data.total
            })

            request.get("/role/all").then(res => {
                this.roles = res.data
            })
        },
        handleUploadSuccess(res) {
            if (res.code === "0") {
                this.$message.success("导入成功")
                this.load()
            }
        },
        exportUser() {
            location.href = "http://" + window.server.filesUploadUrl + ":9090/user/export";
        },
        add() {
            this.dialogVisible = true
            this.form = {}
        },
        save() {
            if (this.form.id) {  // 更新
                request.put("/user", this.form).then(res => {
                    console.log(res)
                    if (res.code === '0') {
                        this.$message({
                            type: "success",
                            message: "更新成功"
                        })
                    } else {
                        this.$message({
                            type: "error",
                            message: res.msg
                        })
                    }
                    this.load() // 刷新表格的数据
                    this.dialogVisible = false  // 关闭弹窗
                })
            } else {  // 新增
                request.post("/user", this.form).then(res => {
                    console.log(res)
                    if (res.code === '0') {
                        this.$message({
                            type: "success",
                            message: "新增成功"
                        })
                    } else {
                        this.$message({
                            type: "error",
                            message: res.msg
                        })
                    }

                    this.load() // 刷新表格的数据
                    this.dialogVisible = false  // 关闭弹窗
                })
            }

        },
        handleEdit(row) {
            this.form = JSON.parse(JSON.stringify(row))
            this.dialogVisible = true
        },
        handleDelete(id) {
            console.log(id)
            request.delete("/user/" + id).then(res => {
                if (res.code === '0') {
                    this.$message({
                        type: "success",
                        message: "删除成功"
                    })
                } else {
                    this.$message({
                        type: "error",
                        message: res.msg
                    })
                }
                this.load()  // 删除之后重新加载表格的数据
            })
        },
        handleSizeChange(pageSize) {   // 改变当前每页的个数触发
            this.pageSize = pageSize
            this.load()
        },
        handleCurrentChange(pageNum) {  // 改变当前页码触发
            this.currentPage = pageNum
            this.load()
        }
    }
}
</script>
