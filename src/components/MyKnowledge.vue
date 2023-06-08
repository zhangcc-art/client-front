<template>
  <div class="myKnowledge">
    <img src="../assets/myHome/add.svg" style="float:right;width:30px;height:30px" @click="handleAdd">
    
    <!-- 我的知识（专家）列名 -->
    <el-row :gutter="20" style="text-align: center;font-size:20px;font-weight: bold ;">
        <el-col :span="4"><div>图片</div></el-col>
        <el-col :span="12"><div>标题</div></el-col>
        <el-col :span="4"><div>时间</div></el-col>
        <el-col :span="4"><div>操作</div></el-col>
    </el-row>
    <!-- 我的知识具体信息 -->
    <el-row :gutter="20" style="text-align: center;margin-top: 30px;" v-for="item in myKnowledge" :key="item" >
       
            <el-col :span="4"><img :src="item.picture" style="width:100%;height: 80px;margin-left: 20px;"  @click="toKnowledgeDetail(item.knowledgeId)"/></el-col>
        <el-col :span="12"  @click="toKnowledgeDetail(item.knowledgeId)"><div>{{item.title}}</div></el-col>
        <el-col :span="4"><div>{{item.createTime}}</div></el-col>
        <el-col :span="4">
            <el-button type="success" @click="handleEdit(item)">编辑</el-button>
            <el-button type="danger" @click="handleDelete(item.knowledgeId)">删除</el-button>
        </el-col>
    
        
    </el-row>
    <!-- 我的知识编辑/添加弹窗 -->
    <el-dialog v-model="editVisible" :title="editTitle" width="50%" >
        <div style="float:left">
            <el-upload v-if="state==0" :action="uploadPicture(this.nowKnowledge.knowledgeId)"   :on-success="successUploadPicture" :show-file-list="false"  list-type="picture"  >
                <img v-if="nowKnowledge.picture" :src="nowKnowledge.picture" class="avatar"  style="width:100px;height: 80px;margin-left: 20px;"/>
                <el-icon v-else class="avatar-uploader-icon" style="width:100px;height: 80px;margin-left: 20px;"><Plus /></el-icon>
            </el-upload>
            <el-upload v-if="state==1" :action="addPicture(this.expertId)"  :on-success="successAddPicture" :show-file-list="false"  list-type="picture"  >
                <img v-if="nowKnowledge.picture" :src="nowKnowledge.picture" class="avatar"  style="width:100px;height: 80px;margin-left: 20px;"/>
                <el-icon v-else class="avatar-uploader-icon" style="width:100px;height: 80px;margin-left: 20px;"><Plus /></el-icon>
            </el-upload>
        </div>
        <div style="font: 24px;font-weight: bold;float:left;margin-left:50px;margin-top: 10px;">标题</div>
        <div style="float:left;margin-left:50px;width: 60%;">
            <el-input v-model="nowKnowledge.title" placeholder="请输入知识标题" size="large"/>
        </div>
        <div style="clear:both"></div>
        <el-input v-model="nowKnowledge.content" :rows="6" type="textarea" placeholder="请输入知识介绍" style="width: 93%;margin-left: 20px;margin-top: 20px;"/>
        <br />
        <el-button type="info" size="large" style="margin-top:20px;margin-left: 20px;" @click="editVisible=false">取消</el-button>
        <el-button type="success" size="large" style="margin-top:20px;margin-left:20px;" @click="submitKnowledgeFun">提交</el-button>
    </el-dialog>
    <!-- 我的知识删除弹窗 -->
    <el-dialog v-model="deleteVisible" title="知识删除" width="30%" center>
    <span>
      确认删除？
    </span>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="deleteVisible = false" size="large">取消</el-button>
        <el-button type="danger" @click="deleteMyKnowledge" size="large">确认</el-button>
      </span>
    </template>
  </el-dialog>
  </div>
</template>

<script>
import {updateKnowledge,addKnowledge,deleteKnowledge} from '../api/knowledge.js'
import {getKnowledgeByExpertId} from '../api/expert.js'
import { ElMessage } from 'element-plus'
import { mapState } from 'vuex';
export default {
    name:'MyKnowledge',
    data(){
        return{
            editTitle:'',//存放弹窗标题
            file:'',
            state:-1,//表明编辑或添加 -1为默认，0表示编辑，1表示添加
            editVisible:false,//控制编辑知识弹窗
            deleteVisible:false,//控制删除知识弹窗
            knowledgeId:'',
            nowKnowledge:{},
            myKnowledge:[
            {
                knowledgeId:1,
                picture:'https://tse1-mm.cn.bing.net/th/id/OIP-C.o0br0ep-JEAsvNQQVadi9AAAAA?w=321&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7',
                title:"土壤最基本的肥力要素是什么",
                createTime:"2023-2-26",
                content:'土壤的肥力是土壤物理、化学和生物性质的综合反应，四大肥力因素有: 养分因素、物理因素、化学因素、生物因素。'
            },
            {
                knowledgeId:2,
                picture:'https://tse4-mm.cn.bing.net/th/id/OIP-C.LgUdC9_gEGyO21jHe_fJYQHaE8?w=232&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7',
                title:"中国农业的鼻祖是谁？有哪些传说？",
                createTime:"2023-2-25",
                content:'后稷，其母姜原为帝喾元妃。姜原出野，见巨人足迹，踏之，践之而动如孕。生一子，以为不祥，弃之隘巷，马牛从他旁边过都不踩它；徙置之林中，适会山林多人，迁之；而弃渠中冰上，飞鸟以其翼覆荐之。姜原以为神，遂收养长之。初欲弃之，因名曰弃。弃为儿童时，好种树麻﹑菽，麻﹑菽。成人后，好耕农，相地之宜，善种谷物稼穑，民皆效法。尧听说，举为农师，天下得其利，有功。弃“教民稼穑”，天下尽得其利，弃之于民，功莫大焉，帝舜封弃于邰，赐号曰：“后稷”。'
            },
            ],
            
        }
    },
    computed:{
        ...mapState(['expertName','expertId'])
    },
    methods:{
        // 获得我的知识
        async getMyKnowledge(){
            getKnowledgeByExpertId(this.expertId).then((res)=>{
                this.myKnowledge=res.data.data
                for(let i=0;i<this.myKnowledge.length;i++){
                    this.myKnowledge[i].createTime=this.myKnowledge[i].createTime.substring(0,10)
                    if(res.data.data[i].picture){
                        if(res.data.data[i].picture.substring(0,4)=='http'){
                            this.myKnowledge[i].picture=res.data.data[i].picture
                        }
                        else{
                            this.myKnowledge[i].picture=this.$store.state.HOST+res.data.data[i].picture
                        }
                    }
                }
            })
        },
        // 处理添加按钮，显示弹窗
        handleAdd(){
            this.nowKnowledge={}
            this.editVisible=true
            this.state=1
            this.editTitle='知识添加'
        },
        // 处理编辑按钮，显示编辑弹窗
        handleEdit(nowKnowledge){
            this.editVisible=true,
            this.state=0
            this.nowKnowledge=nowKnowledge
            this.editTitle='知识编辑'
        },
        //处理删除按钮，显示删除弹窗
        handleDelete(id){
            this.deleteVisible=true,
            this.knowledgeId=id
        },
        uploadPicture(knowledgeId){
            return `${this.$store.state.HOST}/knowledge/updateKnowledge?knowledgeId=${knowledgeId}`
        },
        addPicture(expertId){
            return `${this.$store.state.HOST}/knowledge/addKnowledge?expertId=${expertId}`
        },
        successAddPicture(res){
            console.log(res)
            this.nowKnowledge.picture=this.$store.state.HOST+res.data.picture
            this.nowKnowledge.knowledgeId=res.data.knowledgeId
            this.nowKnowledge.title=res.data.content
        },
        successUploadPicture(res){
            console.log(res)
            this.nowKnowledge.picture=this.$store.state.HOST+res.data
        },
        //更新/上传农业知识
        async submitKnowledgeFun(){
            let submitKnowledge=this.nowKnowledge
            submitKnowledge.expertId=this.expertId
            submitKnowledge.ownName=this.expertName
                updateKnowledge(submitKnowledge).then((res) => {
                if(res.status==200){
                    ElMessage({showClose: true,message: '操作成功',type: 'success',})
                    this.editVisible=false
                }
            }).catch((err)=>{
                ElMessage('操作失败，请检查接口是否正确')
            })
            this.getMyKnowledge()
        },
        //删除农业知识
        async deleteMyKnowledge(){
            deleteKnowledge(this.knowledgeId).then((res)=>{
                if(res.status==200){
                    ElMessage({showClose: true,message: '删除知识成功',type: 'success',})
                }
            }).catch((err)=>{
                ElMessage('删除失败，请检查接口是否正确')
                
            })
            this.deleteVisible=false
            for (let i=0;i<this.myKnowledge.length;i++){
                    // 本地假删除
                    if (this.myKnowledge[i].knowledgeId==this.knowledgeId) this.myKnowledge.splice(i, 1)
                }
        },
        // 跳转相应知识
        toKnowledgeDetail(id){
            this.$router.push({path:'/index/KnowledgeDetail',query:{knowledgeId:id} });
        }
        
    },
    mounted(){
        this.getMyKnowledge()
    }
}
</script>

<style>
.myKnowledge{
    margin-left: 8%;
   margin-right: 15%
}
.el-icon.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  text-align: center;
}
</style>