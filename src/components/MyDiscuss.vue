<template>
    <div v-if="discussKnowledge">
    <div class="discussKnowledge" v-for="item in discussKnowledge" :key="item">
        <img src="../assets/myHome/discuss.svg" style="float:left;height: 50px;width:50px;"/>
        <el-collapse >
            <!-- 用户参与讨论的知识 -->
        <el-collapse-item :title="item.title"  class="discussListknowledge"  >
             <!-- 用户在知识下发布的讨论 -->
            <div class="discussListByKnowledge" v-for="discuss in item.discussList" :key="discuss" >
                <el-icon style="height:30px;width:30px;float:left"><Position /></el-icon>
                <div class="discussContent" @click="toKnowledgeDetail(item.knowledgeId)">{{ discuss.content }}</div>
                <el-icon style="height:30px;width:30px;float:right;" color="#4DB90D" size="20px" @click="handleDiscussDelete(discuss.discussId)"><Delete /></el-icon>
                <div style="font-size: 18px;color:#5c5959;float:right">{{ discuss.createTime }}</div>
                <div style="clear:both"></div>
            </div>
        </el-collapse-item>
    </el-collapse>
    <!-- 删除弹窗 -->
    <el-dialog v-model="discussDeleteVisible" title="知识讨论删除" width="30%" center>
    <span>
      确认删除？
    </span>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="discussDeleteVisible = false" size="large">取消</el-button>
        <el-button type="danger" @click="deleteDiscuss" size="large">确认</el-button>
      </span>
    </template>
  </el-dialog>
    </div>
    </div>
    <div v-else>
        <img src="../assets/myHome/discuss.svg" style="float:left;height: 50px;width:50px;margin-left: 200px;margin-right: 20px;"/>
        <div class="discussListknowledge">您暂未发表过讨论哦</div>
    </div>
</template>

<script>
import {getKnowledgeDiscussByUserId,deleteMyDiscuss} from '../api/knowledge.js'
import { ElMessage } from 'element-plus'
import { mapState } from 'vuex';
export default {
    name:'discussList',
    data(){
        return {
            discussId:-1,//存放点击的讨论id
            discussDeleteVisible:false,//存放知识弹窗控制按钮
            discussKnowledge:[
                {
                    knowledgeId:1,
                    title:'土壤最基本的肥力要素是什么',
                    discussList:[
                        {
                            discussId:1,
                            content:"杨老师能详细讲一下土壤肥力的四大因素嘛",
                            createTime:'2023-3-1'
                        }
                        ,
                        {
                            discussId:2,
                            content:'杨老师真棒',
                            createTime:'2023-3-2'
                        }
                    ],
                },
                {
                    knowledgeId:2,
                    title:'中国农业的鼻祖是谁？有哪些传说',
                    discussList:[
                        {
                            discussId:3,
                            content:"杜老师讲的知识也太有趣了吧",
                            createTime:'2023-3-4'
                        },
                        {
                            discussId:3,
                            content:'杜老师真棒',
                            createTime:'2023-3-4'
                        }
                    ],
                }
            ]
        }
    },
    computed:{
        ...mapState(['userId'])
    },
    methods:{
        // 查找用户发表了讨论的知识及该知识下的讨论
        getMyKnowledgeDiscuss(){
            getKnowledgeDiscussByUserId(this.userId).then((res)=>{
                if(res.status==200){
                    this.discussKnowledge=res.data.data
                    console.log(this.discussKnowledge)
                    if(this.discussKnowledge){
                        for(let i=0;i<this.discussKnowledge.length;i++){
                        for (let j=0;j<this.discussKnowledge[i].discussList.length;j++){
                            this.discussKnowledge[i].discussList[j].createTime=this.discussKnowledge[i].discussList[j].createTime.substring(0,10)
                        }
                    }
                    }
                    
                }
            })
        },
        //响应删除按钮
        handleDiscussDelete(id){
            this.discussDeleteVisible=true,
            this.discussId=id
        },
        // 删除评论
        deleteDiscuss(){
            deleteMyDiscuss(this.discussId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '删除讨论成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('删除失败，请检查接口是否正确')
            })
            // 本地假删除
            for (let i=0;i<this.discussKnowledge.length;i++){
                for (let j=0;j<this.discussKnowledge[i].discussList.length;j++){
                    if (this.discussKnowledge[i].discussList[j].discussId==this.discussId){
                        this.discussKnowledge[i].discussList.splice(j, 1)
                    }
                    
                }
                if(this.discussKnowledge[i].discussList==[]){
                        this.discussKnowledge.splice(i, 1)
                    }
            }
            this.discussDeleteVisible=false
        },
        // 跳转相应知识
        toKnowledgeDetail(id){
            this.$router.push({path:'/index/KnowledgeDetail',query:{knowledgeId:id} });
        }
    },
    mounted(){
        this.getMyKnowledgeDiscuss()
    }
}
</script>

<style >
.discussKnowledge{
    margin-left: 8%;
    margin-right: 15%;
    margin-bottom: 20px;
}
.discussListknowledge .el-collapse-item__header{
  font-size: 20px;
  color: #5c5959;
}
.discussListByKnowledge{
    margin-top: 10px;
    margin-left: 20px;
    
}
.discussContent{
    font-size: 18px;
    font-weight: bold;
    float: left;
}
</style>