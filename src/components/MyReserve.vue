<template>
    <div class="myReserve">
        <!-- 我的预约（列名） -->
        <el-row :gutter="20" style="text-align: center;font-size:20px;font-weight: bold ;">
            <el-col :span="3"><div>预约专家</div></el-col>
            <el-col :span="10"><div>预约标题</div></el-col>
            <el-col :span="6"><div>预约进度</div></el-col>
            <el-col :span="3"><div>操作</div></el-col>
        </el-row>
        <el-row :gutter="20" style="text-align: center;color:#5c5959;font-size:18px;margin-top: 30px;" v-for="item in myReserve" :key="item">
            <!-- 预约专家名 -->
            <el-col :span="3"><div class="reserveExpertName">{{item.expertName}}</div></el-col>
            <!-- 预约标题 -->
            <el-col :span="10"><div class="reserveTitle">{{item.title}}</div></el-col>
            <!-- 预约进度 -->
            <el-col :span="6" style="text-align: left;" >
                <el-timeline style="float:left;margin-right: 10px;">
                    <el-timeline-item :icon="item.nowActivity.icon" :color="item.nowActivity.color" :type="item.nowActivity.type" :timestamp="item.nowActivity.createTime">
                        {{ item.nowActivity.content }}
                    </el-timeline-item>
                 </el-timeline>
                 <!-- 进度弹出框 -->
                 <el-popover  placement="right"  :width="200" trigger="hover">
                    <el-timeline>
                        <el-timeline-item v-for="activity,index in item.activities"
                            :key="index"
                            :icon="activity.icon"
                            :type="activity.type"
                            :color="activity.color"
                            :size="activity.size"
                            :hollow="activity.hollow"
                            :timestamp="activity.createTime"
                            >
                            {{activity.content}}
                        </el-timeline-item>
                    </el-timeline>
                    <template #reference>
                        <img src="../assets/myHome/look.svg" style="height:20px;width:20px;"/>
                    </template>
                </el-popover>
            </el-col>
            <!-- 预约取消 -->
            <el-col :span="3">
                <el-button type="danger" @click="handleDelete(item.reserveId)">取消预约</el-button>
            </el-col>
            <!--  -->
            <el-dialog v-model="deleteVisible" title="预约信息删除" width="30%" center>
                <span>
                确认删除？
                </span>
                <template #footer>
                <span class="dialog-footer">
                    <el-button @click="deleteVisible = false" size="large">取消</el-button>
                    <el-button type="danger" @click="deleteMyReserve" size="large">确认</el-button>
                </span>
                </template>
            </el-dialog>
    </el-row>
    </div>
</template>

<script>
import { Check,Close } from '@element-plus/icons-vue';
import { markRaw } from 'vue'
import { mapState } from 'vuex';
import {getReserve,deleteReserve} from '../api/reserve.js'
import { ElMessage } from 'element-plus'
export default {
    name:'MyReserve',
    data(){
        return{
            reserveId:-1,
            deleteVisible:false,
            myReserve:[
                {
                    reserveId:1,
                    expertName:'刘彩凤',
                    title:'刘老师我想预约您周五当面向您询问一些病毒相关的知识',
                    createTime:'2023-3-1',
                    reserveStatus:-1,
                    nowActivity:{},
                    activities :[]
                },
                {
                    reserveId:2,
                    expertName:'杜云朝',
                    title:'杜老师我想预约您周五当面向您询问一些病毒相关的知识',
                    createTime:'2023-3-2',
                    responseTime:'2023-3-4',
                    reserveStatus:0,
                    nowActivity:{},
                    activities :[]
                }
            ],
        }
    },
    methods:{
        // 获得我的预约
        async getMyReverse(){
            getReserve(this.userId).then((res)=>{
                this.myReserve=res.data.data
                for(let i=0;i<res.data.data.length;i++){
                    if(res.data.data[i].createTime){
                        this.myReserve[i].createTime=res.data.data[i].createTime.substring(0,10)
                    }
                    
                    if(res.data.data[i].responseTime){
                        this.myReserve[i].responseTime=this.myReserve[i].responseTime.substring(0,10)
                    }
                    
                }
                this.changePlan()
            })
            
        },
        // 改变进度展示
        changePlan(){
            for(let i=0;i<this.myReserve.length;i++){
                let date=new Date()
                let year=date.getFullYear()
                let month=date.getMonth()+1
                let day=date.getDate()
                if(Math.floor(month/10)==0){
                    month='0'+month
                }
                if(Math.floor(day/10)==0){
                    day='0'+day
                }

                let time=year+'-'+month+'-'+day
                let activities =[
                        {
                            content: '开始申请',
                            createTime: this.myReserve[i].createTime,
                            size: 'large',
                            type: 'primary',
                            icon: markRaw(Check),
                        },
                        {
                            content: '预约进行中',
                            createTime: this.myReserve[i].createTime,
                            color: '#0bbd87',
                            icon: markRaw(Check),
                        },
                        {
                            content: '预约待专家审核',
                            createTime: '',
                            createTime: time,
                        },
                    ]
                // 预约进行中
                if(this.myReserve[i].reserveStatus==-1){
                    
                    this.myReserve[i].activities =activities
                    
                    this.myReserve[i].nowActivity=this.myReserve[i].activities[1]
                }
                // 预约失败
                if(this.myReserve[i].reserveStatus==0){
                    this.myReserve[i].activities =activities
                    this.myReserve[i].activities[2]={
                        content: '预约未通过',
                            createTime: this.myReserve[i].responseTime,
                            type:'danger',
                            icon:markRaw(Close)
                    }
                    this.myReserve[i].nowActivity=this.myReserve[i].activities[2]
                }
                // 预约成功
                if(this.myReserve[i].reserveStatus==1){
                    this.myReserve[i].activities =activities
                    this.myReserve[i].activities[2] ={
                            content: '预约成功',
                            createTime: this.myReserve[i].responseTime,
                            type:'primary',
                            icon: markRaw(Check),
                    },
                    this.myReserve[i].nowActivity=this.myReserve[i].activities[2]
                }
            }
        },
        //处理删除弹窗
        handleDelete(id){
            this.deleteVisible=true
            this.reserveId=id
        },
        //删除我的预约
        deleteMyReserve(){
            deleteReserve(this.reserveId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '删除预约信息成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('删除失败，请检查接口是否正确')
            })
            for (let i=0;i<this.myReserve.length;i++){
                    // 本地假删除
                    if (this.myReserve[i].reserveId==this.reserveId) this.myReserve.splice(i, 1)
                }
            this.deleteVisible=false
        }
    },
    computed:{
        ...mapState(['userId'])
    },
    mounted(){
        this.getMyReverse()
    }
}
</script>

<style>
.myReserve{
    margin-left: 8%;
   margin-right: 15%
}

</style>