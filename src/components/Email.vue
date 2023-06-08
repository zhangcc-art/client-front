<template>
    <div class="myMail">
        <el-table 
            :row-style="{height: '50px',fontSize:'14px'}"
            :header-cell-style="{fontSize: '20px',  fontWeight: 'bold',color:'black'}" 
            :data="emailData.slice((currentPage-1)*pageSize,currentPage*pageSize)" 
            style="width: 100%;"
            stripe
            >
            <!-- 邮箱tag -->
            <el-table-column  prop="tag" label="类型" width="180" 
                :filters="[{ text: '提问', value: '提问' },{ text: '预约', value: '预约' },{ text: '通知', value: '通知' }]"
                :filter-method="filterTag"
                filter-placement="bottom-end">
                <template #default="scope">
                    <el-tag class="ml-2" type="success" v-if="scope.row.tag=='提问'" style="font-size:14px">{{scope.row.tag}}</el-tag>
                    <el-tag class="ml-2" type="warning" v-if="scope.row.tag=='预约'" style="font-size:14px">{{scope.row.tag}}</el-tag>
                    <el-tag class="ml-2" type="info" v-if="scope.row.tag=='通知'" style="font-size:14px">{{scope.row.tag}}</el-tag>
                </template>
            </el-table-column>
            <!-- 邮箱姓名 -->
            <el-table-column prop="name" label="名字" width="180" />
            <!-- 邮箱信息 -->
            <el-table-column label="信息" width="220" >
                <template #default="scope">
                    <!-- 头像信息气泡浮动 -->
                    <el-popover  placement="left"  :width="240" trigger="hover"  :content="scope.row.title">
                        <template #reference>
                            <div  v-if="scope.row.tag=='提问'">
                                <div style="float: left;">{{ scope.row.info }}</div>
                                <img  @click="handleClick(scope.row)" src="../assets/expert/click.png" style="width:20px;height:20px;margin-right: 10px;"/>
                            </div>
                            <div  v-if="scope.row.tag=='预约'">{{ scope.row.info }}</div>
                            <div  v-if="scope.row.tag=='通知'">{{ scope.row.info }}</div>
                        </template>
                    </el-popover>
                </template>
            </el-table-column>
            <!-- 邮箱时间 -->
            <el-table-column  sortable prop="createTime" label="时间" width="180" />
            <!-- 邮箱操作 -->
            <el-table-column  label="操作" width="260">
                <template #default="scope">
                    <div v-if="scope.row.tag=='提问'">
                        <div  v-if="this.role=='专家'">
                            <el-button  type="success" v-if="!scope.row.answer" @click="handleClick(scope.row)">回复</el-button>
                            <el-button  type="success" v-if="scope.row.answer" >已回复</el-button>
                        </div>
                        <el-button  type="info" v-if="this.role=='农户'" @click="handleDelete(scope.row.questionId)">删除</el-button>
                    </div>
                    <div v-if="scope.row.tag=='预约'">
                        <div v-if="scope.row.reserveStatus==-1">
                            <el-button type="danger" v-if="scope.row.tag=='预约'" @click="rejectTheReserve(scope.row)">拒绝</el-button>
                            <el-button type="warning" v-if="scope.row.tag=='预约'"  @click="acceptTheReserve(scope.row)">同意</el-button>
                        </div>
                        <el-button type="info" plain  v-if="scope.row.reserveStatus==0" >已拒绝</el-button>
                        <el-button type="success" v-if="scope.row.reserveStatus==1" >已接受</el-button>
                    </div>
                    <div v-if="scope.row.tag=='通知'">
                        <div  v-if="scope.row.status==0" >
                            <el-button type="info" plain  style="float:left">未读</el-button>
                            <img src="../assets/myHome/change.svg" style="width:20px;height: 20px;" @click="updateMailState(scope.row)"/>
                        </div>
                        <el-button type="success" v-if="scope.row.status==1" style="float:left">已读</el-button>
                    </div>
                </template>
            </el-table-column>
        </el-table>
             <!-- 专家问答详情弹窗 -->
        <el-dialog v-model="questionVisible" title="问答信息" width="50%" center v-if="this.role=='专家'">
            <el-avatar  :src="questionDetail.userAvatar" style="float:left"/>
            <div class="questionDetailName">{{questionDetail.userName}}</div>
            <div class="bubbleLeft">{{questionDetail.question}}</div>
            <el-avatar  :src="questionDetail.expertAvatar" style="float:right"/>
            <div class="questionDetailName" style="float:right">{{questionDetail.expertName}}</div>
            <div style="clear:both"></div>
            <div class="bubbleRight" style="float: right;">{{questionDetail.answer}}</div>
            <div style="clear:both"></div>
            <div v-if="questionDetail.answer=='您还未回复'">
                <el-input  v-model="answer" :rows="5" type="textarea" placeholder="请输入您的回复" style="margin-top:20px"/>
                <el-button type="info" size="large" style="margin-top:20px;" @click="handleCancelAnswer">取消</el-button>
                <el-button type="success" size="large"  style="margin-top:20px;margin-left: 20px;" @click="submitAnswer">确认回复</el-button>
            </div>
        </el-dialog>
        <!-- 农户问答详情弹窗 -->
        <el-dialog v-model="questionVisible" title="问答信息" width="50%" center v-if="this.role=='农户'">
            <el-avatar  :src="questionDetail.userAvatar" style="float:right"/>
            <div class="questionDetailName" style="float: right;">{{questionDetail.userName}}</div>
            <div style="clear:both"></div>
            <div class="bubbleRight" style="float: right;">{{questionDetail.question}}</div>
            <div style="clear:both"></div>
            <el-avatar  :src="questionDetail.expertAvatar" style="float:left"/>
            <div class="questionDetailName" >{{questionDetail.expertName}}</div>
            <div class="bubbleLeft" >{{questionDetail.answer}}</div>
        </el-dialog>
        <!-- 农户删除知识弹窗 -->
        <el-dialog v-model="deleteVisible" title="问答信息删除" width="30%" center>
            <span>
            确认删除？
            </span>
            <template #footer>
            <span class="dialog-footer">
                <el-button @click="deleteVisible = false" size="large">取消</el-button>
                <el-button type="danger" @click="deleteQuestion" size="large">确认</el-button>
            </span>
            </template>
        </el-dialog>
       
        <el-pagination layout="prev, pager, next" :total="emailData.length"
              class="pageNext"
              @current-change="handleCurrentChange"
              v-model:currentPage="currentPage" /> 
    </div>
</template>
<script>
import { mapState } from 'vuex';
import {getMailByUserId,updateMail} from '../api/email.js'
import {rejectReserve,acceptReserve,getReserveByExpertId} from '../api/reserve.js'
import {deleteQuestion,editAnswer,getQuestionByUserId,getQuestionByExpertId} from '../api/question.js'
import {getExpertDetailById} from '../api/expert.js'
import {getUserDetailById} from '../api/user.js'
import { ElMessage } from 'element-plus'
export default {
    name:'Email',
    inject:['reload'],
    data(){
        return {
            mailUserId:-1,
            mailExpertId:-1,
            deleteVisible:false,//控制问题删除弹窗
            answer:'',//专家回复输入
            questionVisible:false,//控制问题详情弹窗
            questionId:-1,//当前问题id
            questionDetail:{//存放问题详情信息
                expertAvatar:'',
                userAvatar:'https://ts1.cn.mm.bing.net/th?id=OIP-C.OsU-EcE5XtjWon9OpXGU2gAAAA&w=100&h=106&c=8&rs=1&qlt=90&o=6&dpr=1.3&pid=3.1&rm=2',
                userName:'刘大志',
                expertName:'刘彩凤',
                question:'刘老师我想请问土壤的四大成分具体有哪些区别',
                answer:''
            },
            currentPage:1, //默认当前页面为1
            pageSize: 10, //总共有多少数据
            reserveData:[],//暂时存放预约的数据
            mail:[],//暂时存放通知
            emailData:[{
                questionId:1,
                tag:'提问',
                name:'刘大志',
                title:"刘老师我想请问土壤的四大成分具体有哪些区别",
                createTime:'2023-3-3',
            },
            {
                reserveId:1,
                reserveStatus:-1,
                tag:'预约',
                name:'王帅',
                title:"刘老师我想请问预约您面对面教导一下土壤好坏的识别",
                createTime:'2023-3-1'
            },
            {
                mailId:1,
                tag:'通知',
                name:'王帅',
                title:"胡汉三购买了您的一枝花",
                createTime:'2023-3-1',
                status:0
            },
            ]
        }
    },
    computed:{
        ...mapState(['role','expertId','userId','avatar'])
    },
    methods:{
        //处理问题查看详情
        handleClick(question){
            this.questionDetail.answer=this.answer
            this.questionVisible=true
            this.getAvararInfo()
            if( this.role=='专家'){
                this.questionDetail.answer=question.answer?question.answer:"您还未回复"
                this.mailUserId=question.userId
            }
            if( this.role=='农户'){
                this.questionDetail.answer=question.answer?question.answer:"专家还未答复"
                this.mailExpertId=question.expertId
            }
            this.questionDetail.question=question.question?question.question:this.questionDetail.question
            this.questionDetail.userName=question.realName?question.realName:this.questionDetail.userName
            this.questionDetail.expertName=question.expertName?question.expertName:this.questionDetail.expertName
            this.questionId=question.questionId
        },
        // 问答详情界面取消按钮
        handleCancelAnswer(){
            this.questionVisible=false
            this.answer=''
        },
        //处理专家回答
        async submitAnswer(){
            let answer={
                questionId:this.questionId,
                answer:this.answer
            }
            editAnswer(answer).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '回复成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('回复失败，请检查接口是否正确')
            })
            for (let i=0;i<this.emailData.length;i++){
                if (this.emailData[i].questionId==this.questionId) this.emailData[i].answer=this.answer
            }
            this.questionVisible=false
        },
        // 点击删除按钮
        async handleDelete(id){
            this.questionId=id
            this.deleteVisible=true
        },
        // 确认删除
        async deleteQuestion(){
            deleteQuestion(this.questionId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '删除问答信息成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('删除失败，请检查接口是否正确')
            })
            for (let i=0;i<this.emailData.length;i++){
                    // 本地假删除
                    if (this.emailData[i].questionId==this.questionId) this.emailData.splice(i, 1)
                }
            this.deleteVisible=false
        },
        // 拒绝预约
        async rejectTheReserve(item){
            rejectReserve(item.reserveId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '拒绝成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('拒绝失败，请检查接口是否正确')
            })
            item.reserveStatus=0
        },
        //接受预约
        async acceptTheReserve(item){
            acceptReserve(item.reserveId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '接受预约成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('接受失败，请检查接口是否正确')
            })
            item.reserveStatus=1
        },
         //改通知状态为已读
         async updateMailState(item){
            updateMail(item.mailId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '修改通知状态成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('修改失败，请检查接口是否正确')
            })
            item.status=1
        },
        //获得专家或者农户的头像
       async getAvararInfo(){
            // 角色为农户，缓存里已经有农户头像，没有专家头像
            if(this.role=='农户'){
                if(this.avatar){
                        if(this.avatar.substring(0,4)=='http'){
                            this.questionDetail.userAvatar=this.avatar
                        }
                        else{
                            this.questionDetail.userAvatar=this.$store.state.HOST+this.avatar
                        }
                    }
                getExpertDetailById(this.mailExpertId).then((res)=>{
                    if (res.status==200){
                        if(res.data.data.avatar){
                        if(res.data.data.avatar.substring(0,4)=='http'){
                            this.questionDetail.expertAvatar=res.data.data.avatar
                        }
                        else{
                            this.questionDetail.expertAvatar=this.$store.state.HOST+res.data.data.avatar
                        }
                    }
                    }
                })
            }
            // 角色为专家，缓存里已经有专家头像，没有农户头像
            if(this.role=='专家'){
                if(this.avatar){
                        if(this.avatar.substring(0,4)=='http'){
                            this.questionDetail.expertAvatar=this.avatar
                        }
                        else{
                            this.questionDetail.expertAvatar=this.$store.state.HOST+this.avatar
                        }
                    }
                this.questionDetail.expertAvatar=this.avatar
                getUserDetailById(this.mailUserId).then((res)=>{
                    if (res.status==200){
                        if(res.data.data.avatar){
                        if(res.data.data.avatar.substring(0,4)=='http'){
                            this.questionDetail.userAvatar=res.data.data.avatar
                        }
                        else{
                            this.questionDetail.userAvatar=this.$store.state.HOST+res.data.data.avatar
                        }
                    }
                    }
                })
            }

        },
        // 标签筛选
        filterTag(value,row){
            return row.tag==value
        },
        //处理页码变化
        handleCurrentChange (val){
            changePage.currentPage = val;
        },
        async getMyMail(){
            if(this.role=='专家'){
                // 专家查询收到的提问
                getQuestionByExpertId(this.expertId).then((res)=>{
                    this.emailData=res.data.data
                    for(let i=0;i<this.emailData.length;i++){
                        this.emailData[i].name=res.data.data[i].realName
                        this.emailData[i].title=res.data.data[i].question
                        this.emailData[i].tag='提问'
                    }
                    this.showInfo()
                })
                // 专家查询自己的预约
                getReserveByExpertId(this.expertId).then((res)=>{
                    this.reserveData=res.data.data
                    for(let i=0;i<this.reserveData.length;i++){
                        this.reserveData[i].name=this.reserveData[i].realName
                        this.reserveData[i].tag='预约'
                    }
                    this.emailData=this.emailData.concat(this.reserveData)
                    this.showInfo()
                })
                
            }
            // 农户查询收到的回答
            if(this.role=='农户'){
                getQuestionByUserId(this.userId).then((res)=>{
                    this.emailData=res.data.data
                    for(let i=0;i<this.emailData.length;i++){
                        this.emailData[i].tag='提问'
                        if(res.data.data.answer){
                            this.emailData[i].title=res.data.data[i].answer
                            this.emailData[i].createTime=res.data.data[i].answerTime
                        }
                        else{
                            this.emailData[i].title=res.data.data[i].question
                        }
                        this.emailData[i].name=res.data.data[i].expertName
                    }
                    this.showInfo()
                })
                getMailByUserId(this.userId).then((res)=>{
                    this.mail=res.data.data
                    for(let i=0;i<this.mail.length;i++){
                        this.mail[i].tag='通知'
                        this.mail[i].name=res.data.data[i].sourceName
                    }
                    this.emailData=this.emailData.concat(this.mail)
                    this.showInfo()
                })
                
            }
        },
        showInfo(){
            for(let i=0;i<this.emailData.length;i++){
                this.emailData[i].createTime=this.emailData[i].createTime.substring(0,10)
                if(this.emailData[i].title.length>10){
                    this.emailData[i].info=this.emailData[i].title.substring(0,10);
                    this.emailData[i].info=this.emailData[i].info+"..."
                }
                else{
                    this.emailData[i].info=this.emailData[i].title
                }
            }
        }
    },
    mounted(){
        this.getMyMail()
    }
}
</script>

<style>
.myMail{
    margin-left: 4%;
   margin-right: 8%;
   /* width: 80%; */
}
.questionDetailName{
    color: rgba(154, 154, 154, 1);
    font-size: 18px;
    margin-left: 20px;
}
.bubbleLeft {
  margin-top: 20px;
  margin-left:50px;
  position: relative;
  width: 200px;
  background-color: #fff;
  border-radius: 4px;
  padding: 8px;
  box-sizing: border-box;
  filter: drop-shadow(1px 1px 5px #000);
}

.bubbleLeft::after {
  content: "";
  position: absolute;
  top: 25%;
  left: -6px;
  width:  15px;
  height: 15px;
  background: #fff;
  transform: rotate(30deg) skewX(-30deg) scale(1, 0.866);
  z-index: 2;
  border-top-right-radius: 40%;
  z-index: 1;
}
.bubbleRight {
  margin-top: 20px;
  margin-right:50px;
  position: relative;
  width: 200px;
  background-color: #fff;
  border-radius: 4px;
  padding: 8px;
  box-sizing: border-box;
  filter: drop-shadow(1px 1px 5px #000);
}

.bubbleRight::after {
  content: "";
  position: absolute;
  top: 25%;
  right: -6px;
  width:  15px;
  height: 15px;
  background: #fff;
  transform: rotate(30deg) skewX(-30deg) scale(1, 0.866);
  z-index: 2;
  border-top-right-radius: 40%;
  z-index: 1;
}

</style>