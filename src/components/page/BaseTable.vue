<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 基础表格1
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <div class="page-nav"><span>录入</span> <span class="fenge-span"><</span><span>重启录入</span></div>
                <div class="search-content flex1">
                    <el-input v-model="query.name" placeholder="输入查找工作簿名称" class="handle-input flex1"></el-input>
                    <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>

                </div>

                <el-button class="ml20 back-48ad2d" type="primary" icon="el-icon-search" @click="handleSearch">创建新工作簿</el-button>

                <el-select v-model="query.address" placeholder="2020-05-12" class="handle-select ml20">
                    <el-option key="1" label="广东省" value="广东省"></el-option>
                    <el-option key="2" label="湖南省" value="湖南省"></el-option>
                </el-select>
            </div>
            <el-table
                :data="tableData"
                stripe
                class="table list-table"
                ref="multipleTable"
                header-cell-class-name="table-header"
            >
                <el-table-column prop="name" label="数据信息"></el-table-column>
                <el-table-column label="实施日期">
                    <template slot-scope="scope">￥{{scope.row.money}}</template>
                </el-table-column>
                <el-table-column prop="name" label="测试人数"></el-table-column>

                <el-table-column prop="address" label="提示词"></el-table-column>
                <el-table-column label="状态" align="center">
                    <template slot-scope="scope">
                        {{scope.row.state}}
                    </template>
                </el-table-column>

                <el-table-column label="操作" width="250" align="center">
                    <template slot-scope="scope">
                        <div class="option-btn back-color-35ac6b">整列</div>
                        <div class="option-btn back-color-209b9a">增加</div>
                        <div class="option-btn">修改</div>
                        <div class="option-btn">删除</div>

                        <!-- <el-button
                             type="text"
                             icon="el-icon-edit"
                             @click="handleEdit(scope.$index, scope.row)"
                         >编辑</el-button>
                         <el-button
                             type="text"
                             icon="el-icon-delete"
                             class="red"
                             @click="handleDelete(scope.$index, scope.row)"
                         >删除</el-button>-->
                    </template>
                </el-table-column>
            </el-table>
            <!--<div class="pagination">
                <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="query.pageIndex"
                    :page-size="query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
                ></el-pagination>
            </div>-->
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="用户名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import { fetchData } from '../../api/index';
export default {
    name: 'basetable',
    data() {
        return {
            query: {
                address: '',
                name: '',
                pageIndex: 1,
                pageSize: 10
            },
            tableData: [],
            multipleSelection: [],
            delList: [],
            editVisible: false,
            pageTotal: 0,
            form: {},
            idx: -1,
            id: -1
        };
    },
    created() {
        this.getData();
    },
    methods: {
        // 获取 easy-mock 的模拟数据
        getData() {
            fetchData(this.query).then(res => {
                console.log(res);
                this.tableData = [...res.list, ...res.list, ...res.list,...res.list, ...res.list, ...res.list, ...res.list, ...res.list, ...res.list];
                this.pageTotal = res.pageTotal || 50;
            });
        },
        // 触发搜索按钮
        handleSearch() {
            this.$set(this.query, 'pageIndex', 1);
            this.getData();
        },
        // 删除操作
        handleDelete(index, row) {
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
                .then(() => {
                    this.$message.success('删除成功');
                    this.tableData.splice(index, 1);
                })
                .catch(() => {});
        },

        delAllSelection() {
            const length = this.multipleSelection.length;
            let str = '';
            this.delList = this.delList.concat(this.multipleSelection);
            for (let i = 0; i < length; i++) {
                str += this.multipleSelection[i].name + ' ';
            }
            this.$message.error(`删除了${str}`);
            this.multipleSelection = [];
        },
        // 编辑操作
        handleEdit(index, row) {
            this.idx = index;
            this.form = row;
            this.editVisible = true;
        },
        // 保存编辑
        saveEdit() {
            this.editVisible = false;
            this.$message.success(`修改第 ${this.idx + 1} 行成功`);
            this.$set(this.tableData, this.idx, this.form);
        },
        // 分页导航
        handlePageChange(val) {
            this.$set(this.query, 'pageIndex', val);
            this.getData();
        }
    }
};
</script>

<style scoped>
.handle-box {
    margin-bottom: 20px;
}

.handle-select {
    width: 120px;
}

.handle-input {
    width: 300px;
    display: inline-block;
}
.table {
    width: 100%;
    font-size: 14px;
}
.red {
    color: #ff0000;
}
.mr10 {
    margin-right: 10px;
}
.table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
}
.handle-box{
    display: flex;
    justify-content: space-between;
}
.page-nav span{
    font-size: 16px;
    color: #43be7b;
    line-height: 32px;
}
.page-nav span.fenge-span{
    margin: 0 10px;
}
.ml10{
    margin-left: 10px;
}
.ml20{
    margin-left: 20px;
}
    .back-48ad2d{
        background: #48ad2d;
    }
</style>
