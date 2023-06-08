<template>
  <div class="myInfo">
    <!-- 用户头像 -->
    <img :src="this.myAvatar" style="height:150px;width:150px;float:left">

    <!-- 用户昵称、姓名、电话 -->
    <div v-for="item,index in myInfo" :key="index">
        <div v-if="index<3">
            <img :src="item.iconUrl" style="float: left;width: 20px;height: 20px;margin-left: 50px;margin-right: 30px;">
            
            <div v-if="index==1">
                <div style="float: left;color: red;">*</div>
                <div style="color: rgba(154, 154, 154, 1);margin-bottom: 40px;">{{item.content}}</div>
            </div>
            <div v-else>
                <div v-if="!item.inputState" style="color: rgba(154, 154, 154, 1);margin-bottom: 40px;"  @dblclick="updataState(item)">{{item.content}}</div>
                <div style="margin-bottom: 40px;" v-if="item.inputState">
                    <el-input v-model="item.input"   style="width: 30%;float:left;margin-right: 10px;" ></el-input>
                    <el-button type="success" style="float: left;" @click="updateInfo(item,index)">保存</el-button>
                    <el-button type="info" @click="item.inputState=false" >取消</el-button>
                </div>
            </div>
        </div>
    </div>
    <div style="clear:both;margin-top: 40px;"></div>
    <div style="float:left">
        <!-- 用户地址、金钱 -->
    <div v-for="item,index in myInfo" :key="index">
        <div v-if="index==4">
            <img :src="item.iconUrl" style="float: left;width: 20px;height: 20px;margin-right: 30px;">
            <div style="float: left;color: red;">*</div>
            <div  style="color: rgba(189, 49, 36, 1);margin-bottom: 40px;" >￥{{item.content}}</div>
        </div>
        <div v-if="index==3">
            <img :src="item.iconUrl" style="float: left;width: 20px;height: 20px;margin-right: 30px;">
            <div v-if="!item.inputState" style="color: rgba(154, 154, 154, 1);margin-bottom: 40px;"  @dblclick="updataState(item)">{{item.content}}</div>
        </div>
        <div style="margin-bottom: 40px;" v-if="item.inputState">
            <el-input v-model="item.input"   style="width: 40%;float:left;margin-right: 10px;" ></el-input>
            <el-button type="success" style="float: left;" @click="updateInfo(item,index)">保存</el-button>
            <el-button type="info" @click="item.inputState=false" >取消</el-button>
        </div>
    </div>
        <div style="color: rgba(16, 16, 16, 1);font-weight: bold;">注：双击所选信息可进行修改</div>
    </div>
    <el-upload v-if="this.role=='专家'" :action="uploadExpertUrl(this.expertId)" :show-file-list="false"  :on-success="showMyInfo"  style="float: right;width: 150px;height: 150px;margin-right: 150px;">
        <img src="../assets/myHome/uploadPicture.png" style="height: 100%;width:100%">
    </el-upload>
    <el-upload v-if="this.role=='农户'" :action="uploadUrl(this.userId)" :show-file-list="false"  :on-success="showMyInfo" style="float: right;width: 150px;height: 150px;margin-right: 150px;">
        <img src="../assets/myHome/uploadPicture.png" style="height: 100%;width:100%">
    </el-upload>
  </div>
</template>

<script>
import { ElMessage } from 'element-plus'
import { mapState } from 'vuex';
import {updateNickName,updatePhone,updateAddress,updateMoney,getUserDetailById} from '../api/user'
import {updateExpertNickName,updateExpertPhone,updateExpertAddress,getExpertDetailById} from '../api/expert'

export default {
    name:'MyInfo',
    data(){
        return{
            myAvatar:'',
            // 存放个人信息，数组按顺序为昵称、姓名、电话、地址、钱
            myInfo:[
                {content:'',inputState:false,input:'',iconUrl:require('../assets/myHome/person.svg')},
                {content:'',inputState:false,input:'',iconUrl:require('../assets/myHome/at.svg')},
                {content:'',inputState:false,input:'',iconUrl:require('../assets/myHome/phone.svg')},
                {content:'',inputState:false,input:'',iconUrl:require('../assets/myHome/address.svg')},
            ]
        }
    },
    methods:{
        // 双击信息状态
        updataState(item){
            item.inputState=true
            item.input=item.content
        },
        uploadExpertUrl(expertId){
            return `${this.$store.state.HOST}/user/updateExpertAvatar?expertId=${expertId}`
        },
        uploadUrl(userId){
            return `${this.$store.state.HOST}/user/updateUserAvatar?userId=${userId}`
        },
        // 展示个人信息
        async showMyInfo(){
            if(this.role=='专家'){
                getExpertDetailById(this.expertId).then((res)=>{
                    if(res.status==200){
                        if(res.data.data.avatar){
                            if(res.data.data.avatar.substring(0,4)=='http'){
                                this.myAvatar=res.data.data.avatar
                            }
                            else{
                                this.myAvatar=this.$store.state.HOST+res.data.data.avatar
                            }
                        }
                        else{
                            this.myAvatar='https://tse3-mm.cn.bing.net/th/id/OIP-C.2d_hq2wfNFv4vooZ-TE8vQAAAA?w=180&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7'
                        }
                        this.$store.state.avatar=this.myAvatar;
                        this.myInfo[0].content=res.data.data.nickName?res.data.data.nickName:'您还没有昵称'
                        this.$store.state.nickName=this.myInfo[0].content
                        this.myInfo[2].content=res.data.data.phone?res.data.data.phone:'您还未填写手机号'
                        this.$store.state.phone=this.myInfo[2].content
                        this.myInfo[3].content=res.data.data.address?res.data.data.address:'您还未填地址'
                        this.$store.state.address=this.myInfo[3].content
                        this.myInfo[1].content=res.data.data.expertName
                        this.$store.state.expertName=this.myInfo[1].content
                    }
                })
            }
            if(this.role=='农户'){
                getUserDetailById(this.userId).then((res)=>{
                    if(res.status==200){
                        if(res.data.data.avatar){
                            if(res.data.data.avatar.substring(0,4)=='http'){
                                this.myAvatar=res.data.data.avatar
                            }
                            else{
                                this.myAvatar=this.$store.state.HOST+res.data.data.avatar
                            }
                        }
                        else{
                            this.myAvatar='https://tse3-mm.cn.bing.net/th/id/OIP-C.2d_hq2wfNFv4vooZ-TE8vQAAAA?w=180&h=180&c=7&r=0&o=5&dpr=1.3&pid=1.7'
                        }
                        this.$store.state.avatar=this.myAvatar;
                        this.myInfo[0].content=res.data.data.nickName?res.data.data.nickName:'您还没有昵称'
                        this.$store.state.nickName=this.myInfo[0].content
                        this.myInfo[2].content=res.data.data.phone?res.data.data.phone:'您还未填写手机号'
                        this.$store.state.phone=this.myInfo[2].content
                        this.myInfo[3].content=res.data.data.address?res.data.data.address:'您还未填地址'
                        this.$store.state.address=this.myInfo[3].content
                        this.myInfo[1].content=res.data.data.realName
                        this.$store.state.userName=this.myInfo[1].content
                        this.myInfo.push({content:res.data.data.money,inputState:false,input:'',iconUrl:require('../assets/myHome/money.svg')})
                    }
                })
            }
        },
        //修改信息
        async updateInfo(item,index){
            let input=item.input
            if(this.role=='农户'){
                // 昵称
                if(index==0){
                    let info={nickName:input,userId:this.userId}
                    updateNickName(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[0].content=input
                            this.$store.state.nickName=input;
                            ElMessage({showClose: true,message: '更新昵称成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新昵称失败，请检查接口是否正确')})
                    item.inputState=false
                }
                // 电话
                if(index==2){
                    let info={phone:input,userId:this.userId}
                    updatePhone(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[2].content=input
                            this.$store.state.phone=input;
                            ElMessage({showClose: true,message: '更新手机号成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新手机号失败，请检查接口是否正确')})
                    item.inputState=false
                }
                if(index==3){
                    let info={address:input,userId:this.userId}
                    updateAddress(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[3].content=input
                            this.$store.state.address=input;
                            ElMessage({showClose: true,message: '更新地址成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新地址失败，请检查接口是否正确')})
                    item.inputState=false
                }
                if(index==4){
                    let info={money:input,userId:this.userId}
                    updateMoney(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[4].content=input
                            this.$store.state.money=input;
                            ElMessage({showClose: true,message: '更新金钱成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新金钱失败，请检查接口是否正确')})
                    item.inputState=false
                }  
            }
            if(this.role=='专家'){
                // 昵称
                if(index==0){
                    let info={nickName:input,userId:this.expertId}
                    updateExpertNickName(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[0].content=input
                            this.$store.state.nickName=input;
                            ElMessage({showClose: true,message: '更新昵称成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新昵称失败，请检查接口是否正确')})
                    item.inputState=false
                }
                // 电话
                if(index==2){
                    let info={phone:input,userId:this.expertId}
                    updateExpertPhone(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[2].content=input
                            this.$store.state.phone=input;
                            ElMessage({showClose: true,message: '更新手机号成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新手机号失败，请检查接口是否正确')})
                    item.inputState=false
                }
                if(index==3){
                    let info={address:input,userId:this.expertId}
                    updateExpertAddress(info).then((res)=>{
                        if(res.status==200){
                            this.myInfo[3].content=input
                            this.$store.state.address=input;
                            ElMessage({showClose: true,message: '更新地址成功',type: 'success',})
                        }
                    }).catch((err)=>{ElMessage('更新地址失败，请检查接口是否正确')})
                    item.inputState=false
                }
            }
        }
    },
    computed:{
        ...mapState(['userId','userName','role','nickName','expertName','expertId','avatar','phone','address','money'])
    },
    mounted(){
        this.showMyInfo()
        console.log(this.expertId)
        console.log(this.role)
    }
}
</script>

<style>
.myInfo{
   margin-left: 10%;
   margin-right: 15%
}
</style>