<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-calendar"></i> 表单</el-breadcrumb-item>
                <el-breadcrumb-item>编辑器</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="plugins-tips">
                <h2 class="title">比赛发布</h2>
            </div>
            <div class="container">
                <div class="form-box-contest">
                    <el-form ref="form" :model="form" label-width="280px">
                        <el-form-item label="比赛名称">
                            <el-input v-model="form.contestName"></el-input>
                        </el-form-item>
                        <el-form-item label="作者">
                            <el-input v-model="form.contestAuthor"></el-input>
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
                                        v-model="form.contestApplyTime"
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
                                        v-model="form.contestApplyEndTime"
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
                                        v-model="form.contestStartTime"
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
                                        v-model="form.contestEndTime"
                                        value-format="HH:mm:ss"
                                        style="width: 100%;"
                                ></el-time-picker>
                            </el-col>
                        </el-form-item>
                        <el-form-item label="比赛缩略图">
                            <el-upload
                                    action="http://localhost:8090/api/contest/upload"
                                    list-type="picture-card"
                                    show-file-list
                                    :on-success="this.onFileUploadSuccess"
                                    :file-list="this.fileList"
                                    :auto-upload="true"
                                    :limit="1">
                                <i slot="default" class="el-icon-plus"></i>
                                <div slot="file" slot-scope="{file}">
                                    <img
                                            class="el-upload-list__item-thumbnail"
                                            :src="file.url" alt=""
                                    >
                                    <span class="el-upload-list__item-actions">
                                    <span
                                            class="el-upload-list__item-preview"
                                            @click="handlePictureCardPreview(file)"
                                    >
                                      <i class="el-icon-zoom-in"></i>
                                    </span>
                                    <span
                                            v-if="!disabled"
                                            class="el-upload-list__item-delete"
                                            @click="handleRemove(file, fileList)"
                                    >
                                      <i class="el-icon-delete"></i>
                                    </span>
                                  </span>
                                </div>
                            </el-upload>
                            <el-dialog :visible.sync="dialogVisible">
                                <img width="100%" :src="dialogImageUrl" alt="">
                            </el-dialog>
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

<!--            <quill-editor ref="myTextEditor" v-model="content" :options="editorOption"></quill-editor>-->
            <el-button class="editor-btn" type="primary" @click="submit">提交</el-button>
        </div>
    </div>
</template>

<script>
    import 'quill/dist/quill.core.css';
    import 'quill/dist/quill.snow.css';
    import 'quill/dist/quill.bubble.css';
    import { quillEditor } from 'vue-quill-editor';
    import service from '../../utils/request';
    export default {
        name: 'editor',
        data: function(){
            return {
                content: '',
                editorOption: {
                    placeholder: 'Hello World'
                },
                dialogImageUrl: '',
                dialogVisible: false,
                disabled: false,
                fileList: [],
                form: {
                    contestName: '',
                    contestAuthor: '',
                    contestApplyDate: '',
                    contestApplyTime: '',
                    contestApplyEndDate: '',
                    contestApplyEndTime: '',
                    contestAddress: '',
                    contactUser: '',
                    contactPhone: '',
                    contestStartDate: '',
                    contestStartTime: '',
                    contestEndDate: '',
                    contestEndTime: '',
                    contestThumb: '',
                    contestCooperation: '',
                    contestIntroduce: '',
                    contestReward: '',
                    contestApplyRule: '',
                    contestProcess: '',
                    contestInfo: ''
                }
            }
        },
        components: {
            quillEditor
        },
        methods: {
            onEditorChange({ editor, html, text }) {
                this.content = html;
            },
            onFileUploadSuccess(response, file, fileList) {
                this.fileList = fileList;
                this.form.contestThumb = file.response.data;
            },
            handleRemove(file, fileList) {
                //先删缓存
                fileList.splice(0, 1);
                //删除地址
                this.form.contestThumb = "";
                //发起请求删文件
                if (file.response) {
                    service.post('/contest/deleteImg', {
                        fileUrl: file.response.data
                    });
                }
            },
            handlePictureCardPreview(file) {
                console.log(file);
                this.dialogImageUrl = file.url;
                this.dialogVisible = true;
            },
            handleDownload(file) {
                console.log(file);
            },
            submit(){
                // 提交数据
                console.log(this.form.contestApplyDate + this.form.contestApplyTime )
                service.post('/contest/publish', {
                    'contestName': this.form.contestName,
                    'author': this.form.contestAuthor,
                    'contestApplyTime': this.form.contestApplyDate + " "  + this.form.contestApplyTime,
                    'contestApplyEndTime': this.form.contestApplyEndDate + " " + this.form.contestApplyEndTime,
                    'contestAddress': this.form.contestAddress,
                    'contactUser': this.form.contactUser,
                    'contactPhone': this.form.contactPhone,
                    'contestStartTime': this.form.contestStartDate + " " + this.form.contestStartTime,
                    'contestEndTime': this.form.contestEndDate + " " + this.form.contestEndTime,
                    'contestCooperation': this.form.contestCooperation,
                    'contestIntroduce': this.form.contestIntroduce,
                    'contestReward': this.form.contestReward,
                    'contestApplyRule': this.form.contestApplyRule,
                    'contestProcess': this.form.contestProcess,
                    'contestInfo': this.form.contestInfo,
                    'contestThumb': this.form.contestThumb
                }).then(response => {
                    console.log(response)
                   if (response.msg === "success") {
                       this.$message.success('提交成功！');
                   } else {
                       this.$message.error("提交失败!");
                   }
                });
            }
        }
    }
</script>
<style scoped>
    .editor-btn{
        margin-top: 20px;
    }
    .title {
        text-align: center;
    }
    .form-box-contest {
        width: 1320px;
    }
    .el-form-item__label {
        font-size: 16px;
    }
    .el-form-item__content {
        margin-left: 180px;
    }
</style>
