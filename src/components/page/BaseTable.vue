<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 比赛表格数据
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
<!--                <el-button-->
<!--                    type="primary"-->
<!--                    icon="el-icon-delete"-->
<!--                    class="handle-del mr10"-->
<!--                    @click="delAllSelection"-->
<!--                >批量删除</el-button>-->
<!--                <el-select v-model="query.address" placeholder="地址" class="handle-select mr10">-->
<!--                    <el-option key="1" label="广东省" value="广东省"></el-option>-->
<!--                    <el-option key="2" label="湖南省" value="湖南省"></el-option>-->
<!--                </el-select>-->
<!--                <el-input v-model="query.name" placeholder="用户名" class="handle-input mr10"></el-input>-->
<!--                <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>-->
            </div>
            <el-table
                :data="tableData"
                border
                class="table"
                ref="multipleTable"
                header-cell-class-name="table-header"
                @selection-change="handleSelectionChange"
            >
                <el-table-column type="selection" width="55" align="center"></el-table-column>
                <el-table-column prop="contestId" label="ID" width="55" align="center"></el-table-column>
                <el-table-column prop="contestName" label="比赛名称"></el-table-column>
                <el-table-column label="比赛缩略图" align="center">
                    <template slot-scope="scope">
                        <el-image
                            class="table-td-thumb"
                            :src="scope.row.contestThumb"
                            :preview-src-list="[scope.row.contestThumb]"
                        ></el-image>
                    </template>
                </el-table-column>
                <el-table-column label="联系人">
                    <template slot-scope="scope">{{scope.row.contactUser}}-{{scope.row.contactPhone}}</template>
                </el-table-column>
                <el-table-column prop="contestAddress" label="比赛地址"></el-table-column>
                <el-table-column prop="contestApplyEndTime" label="报名截止时间"></el-table-column>
                <el-table-column prop="contestEndTime" label="比赛截止时间"></el-table-column>
                <el-table-column label="当前状态" align="center">
                    <template slot-scope="scope">
                        <el-tag>{{handleState(scope.row.contestApplyTime, scope.row.contestApplyEndTime,
                            scope.row.contestStartTime, scope.row.contestEndTime)}}</el-tag>
                    </template>
                </el-table-column>
                <el-table-column label="操作" width="180" align="center">
                    <template slot-scope="scope">
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.$index, scope.row)"
                        >编辑</el-button>
                        <el-button
                                type="text"
                                icon="el-icon-connection"
                                @click="handleTeam(scope.$index, scope.row)"
                        >配对</el-button>
                        <el-button
                            type="text"
                            icon="el-icon-download"
                            @click="handleDelete(scope.$index, scope.row)"
                        >下载</el-button>
<!--                        <el-button-->
<!--                                type="text"-->
<!--                                icon="el-icon-download"-->
<!--                                class="red"-->
<!--                                @click="handleDelete(scope.$index, scope.row)"-->
<!--                        >下载</el-button>-->
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="query.pageIndex"
                    :page-size="query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
                ></el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="70%">
            <div class="container">
                <div class="form-box-contest">
                    <el-form ref="form" :model="form" label-width="120px">
                        <el-form-item label="比赛名称">
                            <el-input v-model="form.contestName"></el-input>
                        </el-form-item>
                        <el-form-item label="作者">
                            <el-input v-model="form.author"></el-input>
                        </el-form-item>
                        <el-form-item label="报名开始时间">
                            <el-col :span="11">
                                <el-date-picker
                                        type="date"
                                        placeholder="选择日期"
                                        v-model="form.contestApplyDate"
                                        value-format="yyyy-MM-dd"
                                        style="width: 100%;"
                                ></el-date-picker>
                            </el-col>
                            <el-col class="line" :span="2">-</el-col>
                            <el-col :span="11">
                                <el-time-picker
                                        placeholder="选择时间"
                                        v-model="form.contestApplyTimes"
                                        value-format="HH:mm:ss"
                                        style="width: 100%;"
                                ></el-time-picker>
                            </el-col>
                        </el-form-item>
                        <el-form-item label="报名结束时间">
                            <el-col :span="11">
                                <el-date-picker
                                        type="date"
                                        placeholder="选择日期"
                                        v-model="form.contestApplyEndDate"
                                        value-format="yyyy-MM-dd"
                                        style="width: 100%;"
                                ></el-date-picker>
                            </el-col>
                            <el-col class="line" :span="2">-</el-col>
                            <el-col :span="11">
                                <el-time-picker
                                        placeholder="选择时间"
                                        v-model="form.contestApplyEndTimes"
                                        value-format="HH:mm:ss"
                                        style="width: 100%;"
                                ></el-time-picker>
                            </el-col>
                        </el-form-item>
                        <el-form-item label="比赛地址">
                            <el-input v-model="form.contestAddress"></el-input>
                        </el-form-item>
                        <el-form-item label="联系人">
                            <el-input v-model="form.contactUser"></el-input>
                        </el-form-item>
                        <el-form-item label="联系人电话">
                            <el-input v-model="form.contactPhone"></el-input>
                        </el-form-item>
                        <el-form-item label="比赛开始时间">
                            <el-col :span="11">
                                <el-date-picker
                                        type="date"
                                        placeholder="选择日期"
                                        v-model="form.contestStartDate"
                                        value-format="yyyy-MM-dd"
                                        style="width: 100%;"
                                ></el-date-picker>
                            </el-col>
                            <el-col class="line" :span="2">-</el-col>
                            <el-col :span="11">
                                <el-time-picker
                                        placeholder="选择时间"
                                        v-model="form.contestStartTimes"
                                        value-format="HH:mm:ss"
                                        style="width: 100%;"
                                ></el-time-picker>
                            </el-col>
                        </el-form-item>
                        <el-form-item label="比赛结束时间">
                            <el-col :span="11">
                                <el-date-picker
                                        type="date"
                                        placeholder="选择日期"
                                        v-model="form.contestEndDate"
                                        value-format="yyyy-MM-dd"
                                        style="width: 100%;"
                                ></el-date-picker>
                            </el-col>
                            <el-col class="line" :span="2">-</el-col>
                            <el-col :span="11">
                                <el-time-picker
                                        placeholder="选择时间"
                                        v-model="form.contestEndTimes"
                                        value-format="HH:mm:ss"
                                        style="width: 100%;"
                                ></el-time-picker>
                            </el-col>
                        </el-form-item>
                        <el-form-item label="合作方">
                            <el-input v-model="form.contestCooperation"></el-input>
                        </el-form-item>
                        <el-form-item label="比赛介绍">
                            <quill-editor ref="myTextEditor" v-model="form.contestIntroduce" :options="editorOption"></quill-editor>
                        </el-form-item>
                        <el-form-item label="比赛奖励">
                            <quill-editor ref="myTextEditor" v-model="form.contestReward" :options="editorOption"></quill-editor>
                        </el-form-item>
                        <el-form-item label="报名规则">
                            <quill-editor ref="myTextEditor" v-model="form.contestApplyRule" :options="editorOption"></quill-editor>
                        </el-form-item>
                        <el-form-item label="比赛流程">
                            <quill-editor ref="myTextEditor" v-model="form.contestProcess" :options="editorOption"></quill-editor>
                        </el-form-item>
                        <el-form-item label="比赛说明">
                            <quill-editor ref="myTextEditor" v-model="form.contestInfo" :options="editorOption"></quill-editor>
                        </el-form-item>
                    </el-form>
                </div>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    import service from '../../utils/request';
    import 'quill/dist/quill.core.css';
    import 'quill/dist/quill.snow.css';
    import 'quill/dist/quill.bubble.css';
    import { quillEditor } from 'vue-quill-editor';

    export default {
    name: 'basetable',
    data() {
        return {
            query: {
                pageIndex: 1,
                pageSize: 10
            },
            contestState: {
              noStartApply: '未开始报名',
              canApply: '正在报名',
              completeApply: '报名结束，等待开赛',
              contesting: '正在比赛',
              endContest: '报名结束'
            },
            editorOption: {
                placeholder: 'Hello World'
            },
            // form: {
            //     contestName: '',
            //     contestAuthor: '',
            //     contestApplyDate: '',
            //     contestApplyTime: '',
            //     contestApplyEndDate: '',
            //     contestApplyEndTime: '',
            //     contestAddress: '',
            //     contactUser: '',
            //     contactPhone: '',
            //     contestStartDate: '',
            //     contestStartTime: '',
            //     contestEndDate: '',
            //     contestEndTime: '',
            //     contestThumb: '',
            //     contestCooperation: '',
            //     contestIntroduce: '',
            //     contestReward: '',
            //     contestApplyRule: '',
            //     contestProcess: '',
            //     contestInfo: ''
            // },
            pageTotal: 0,
            currentPage: '',
            tableData: [],
            multipleSelection: [],
            delList: [],
            editVisible: false,
            form: {},
            idx: -1,
            id: -1
        };
    },
    components: {
        quillEditor
    },
    created() {
        this.getData(this.query.pageIndex, this.query.pageSize);
    },
    methods: {
        // 获取 easy-mock 的模拟数据
        getData(pageNum, pageSize) {
            service.get('/contest/getContest?' + "pageNum=" + pageNum + "&pageSize=" + pageSize).then(res => {
                this.pageTotal  = res.data.total;
                console.log(res.data.list)
                res.data.list.map((post) => {
                    post.contestApplyDate = this.formatDate(post.contestApplyTime);
                    post.contestApplyTimes = this.formatTime(post.contestApplyTime);
                    post.contestApplyEndDate = this.formatDate(post.contestApplyEndTime);
                    post.contestApplyEndTimes = this.formatTime(post.contestApplyEndTime);
                    post.contestStartDate = this.formatDate(post.contestStartTime);
                    post.contestStartTimes = this.formatTime(post.contestStartTime);
                    post.contestEndDate = this.formatDate(post.contestEndTime);
                    post.contestEndTimes = this.formatTime(post.contestEndTime);
                });
                this.tableData = res.data.list;
                console.log(this.tableData)
            });
        },
        // 触发搜索按钮
        handleSearch() {
            this.$set(this.query, 'pageIndex', 1);
            this.getData();
        },
        handleTeam(index, row) {

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
        // 多选操作
        handleSelectionChange(val) {
            this.multipleSelection = val;
        },
        // onFileUploadSuccess(response, file, fileList) {
        //     this.fileList = fileList;
        //     this.form.contestThumb = file.response.data;
        // },
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
        formatDate(dates) {
            let date = new Date(dates);
            var myyear = date.getFullYear();
            var mymonth = date.getMonth() + 1;
            var myweekday = date.getDate();
            if (mymonth < 10) {
                mymonth = "0" + mymonth;
            }
            if (myweekday < 10) {
                myweekday = "0" + myweekday;
            }
            return (myyear + "-" + mymonth + "-" + myweekday);//想要什么格式都可以随便自己拼
        },
        formatTime (times) {
            let date = new Date(times);
            let hour = date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
            let minute = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
            let second = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
            return hour + ':' + minute + ':' + second;
        },
        // 保存编辑
        saveEdit() {
            this.editVisible = false;
            this.$message.success(`修改第 ${this.idx + 1} 行成功`);
            this.form.contestApplyTime = this.form.contestApplyDate + " " + this.form.contestApplyTimes;
            this.form.contestApplyEndTime = this.form.contestApplyEndDate + " " + this.form.contestApplyEndTimes;
            this.form.contestStartTime = this.form.contestStartDate + " " + this.form.contestStartTimes;
            this.form.contestEndTime = this.form.contestEndDate + " " + this.form.contestEndTimes;
            this.$set(this.tableData, this.idx, this.form);

            service.post('/contest/updateContest', {
                'contestId': this.form.contestId,
                'contestName': this.form.contestName,
                'author': this.form.contestAuthor,
                'contestApplyTime': this.form.contestApplyTime,
                'contestApplyEndTime': this.form.contestApplyEndTime,
                'contestAddress': this.form.contestAddress,
                'contactUser': this.form.contactUser,
                'contactPhone': this.form.contactPhone,
                'contestStartTime': this.form.contestStartTime,
                'contestEndTime': this.form.contestEndTime,
                'contestCooperation': this.form.contestCooperation,
                'contestIntroduce': this.form.contestIntroduce,
                'contestReward': this.form.contestReward,
                'contestApplyRule': this.form.contestApplyRule,
                'contestProcess': this.form.contestProcess,
                'contestInfo': this.form.contestInfo
            });
            // this.editVisible = false;
            // this.$message.success(`修改第 ${this.idx + 1} 行成功`);
            // this.$set(this.tableData, this.idx, this.form);
            // console.log(this.idx)
            // console.log(this.form)
            // console.log(this.tableData);

        },
        // 分页导航
        handlePageChange(val) {
            this.$set(this.query, 'pageIndex', val);
            this.getData(this.query.pageIndex, this.query.pageSize);
        },
        // 处理状态
        handleState(applyTime, applyEndTime, startContestTime, endContestTime) {
            let now = new Date();
            applyTime = new Date(applyTime);
            applyEndTime = new Date(applyEndTime);
            startContestTime = new Date(startContestTime);
            endContestTime = new Date(endContestTime);
            if (now < applyTime) {
                return this.contestState.noStartApply;
            } else if (applyTime < now && now < applyEndTime) {
                return this.contestState.canApply;
            } else if (applyEndTime < now && now < startContestTime) {
                return this.contestState.completeApply;
            } else if (startContestTime < now && now < endContestTime) {
                return this.contestState.contesting;
            } else if (endContestTime < now) {
                return this.contestState.endContest;
            }
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
</style>
