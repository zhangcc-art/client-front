<template>
    <div class="header">
      <img src="../assets/header/logo.png" style="height:40px;width:40px;margin-left: 20px;margin-top: 20px;float:left"/>
      <img src="../assets/header/title.png" style="height:50px;width:100px;margin-left: 10px;margin-top: 10px;float:left"/>
      <!-- 导航栏 -->
      <div  v-for="item,index in title" :key="item" >
        <div  v-if="index==this.current" class="headerTitleActive"> {{item.name}}</div>
        <div  v-else class="headerTitle" @click="change(index,item.href)"> {{item.name}}</div>
      </div>
      <!-- 头像 -->
      <el-popover  placement="bottom"  :width="200" trigger="hover">
        <div style="margin-top: 10px;">
          <div @click="change(8,'/index/myHome/myInfo')" style="margin-bottom: 20px;">
            <img src="../assets/header/person.svg" style="width: 30px;height: 30px;float: left;margin-right: 20px;"/>
            <div style="font-size: 20px;">个人中心</div>
          </div>
          <div style="clear:both"></div>
          <div v-if="loginState==false" @click="toLogin">
            <img src="../assets/header/login.svg"  style="width: 30px;height: 30px;float: left;margin-right: 20px;"/>
            <div style="font-size: 20px;">登录</div>
          </div>
          <div v-if="loginState==true"  @click="logOut">
            <img src="../assets/header/logout.svg" style="width: 30px;height: 30px;float: left;margin-right: 20px;"/>
            <div style="font-size: 20px;">登出</div>
          </div>
          
          <div style="clear:both"></div>
        </div>
        <template #reference>
          <el-avatar :src="this.avatar" class="headerAvatar" />
        </template>
      </el-popover>
      
      <!-- 铃铛通知 -->
      <div class="mailLength">{{mailLength }}</div>
      <img v-if="this.current==7" src="../assets/header/bellActive.svg" class="headerBell"/>
      <img v-else src="../assets/header/bell.svg" class="headerBell" @click="change(7,'/index/myHome/email')" style="cursor:pointer;"/>
    
      
      <!-- 购物车 -->
      <img v-if="this.current==6" src="../assets/header/shoppingcartActive.svg" class="headerShpping" />
      <img v-else src="../assets/header/shoppingcart.svg" class="headerShpping" @click="change(6,'/index/shoppingcart')"/>
      <div style="clear:both"></div>
    </div>
</template>

<script>
import { mapState } from 'vuex';
import { ElMessage } from 'element-plus'
export default {
    name:"Header",
    data(){
        return {
          loginState:false,
          title:[{
            name:'首页',
            href:'/index/home'
          },
          {
            name:'商品货源',
            href:'/index/productSource',
          },
          {
            name:'求购需求',
            href:'/index/productNeeds',
          },
          {
            name:'农业知识',
            href:'/index/knowledge'
          },
          {
            name:'专家指导',
            href:'/index/expert'
          },
          {
            name:'融资',
            href:'/index/financing'
          }
        ],
          // myAvatar:'https://tse4-mm.cn.bing.net/th/id/OIP-C.bAZhN4PcSogKHreHIJlDVwAAAA?w=203&h=203&c=7&r=0&o=5&dpr=1.3&pid=1.7',
          mailLength:5
        }
    },
    methods:{
      change(index,href){
        console.log(this.loginState)
        if(this.loginState){
          this.$router.push(href)
          this.$store.state.current=index;
        }
        else{
          if(index==6){
            console.log("6")
            ElMessage('您未登录，即将跳转到登录页面')
            this.$router.push('/userLogin')
            console.log("7")
          }
          else if(index==7){
              ElMessage('您未登录，即将跳转到登录页面')
              this.$router.push('/userLogin')
          }
          else if(index==8){
              ElMessage('您未登录，即将跳转到登录页面')
              this.$router.push('/userLogin')
          }
          else{
            this.$router.push(href)
            this.$store.state.current=index;
          }
        }
        
        
	    },
      isLogin(){
        if(localStorage.getItem('token')!=""){
          this.loginState=true
        }
        else{
          this.$store.state.avatar='https://tse2-mm.cn.bing.net/th/id/OIP-C.cEZgJMPTl2tKbEXHGT4LCAAAAA?w=148&h=155&c=7&r=0&o=5&dpr=1.3&pid=1.7'
          this.loginState=false
        }
      },
      toLogin(){
        this.$router.push('/userLogin');
      },
      logOut(){
        this.$store.state.current=-1;
        console.log("aa")
        localStorage.removeItem('token')
        if(this.$store.state.role=='农户'){
            this.$store.state.userId=-1
        }
        if(this.$store.state.role=='专家'){
          this.$store.state.expertId=-1
        }
        this.$store.state.role=''
        this.$store.state.avatar='https://tse2-mm.cn.bing.net/th/id/OIP-C.cEZgJMPTl2tKbEXHGT4LCAAAAA?w=148&h=155&c=7&r=0&o=5&dpr=1.3&pid=1.7'
        this.loginState=false
      }
    },
    computed:{
        ...mapState(['current','avatar'])
    },
    mounted(){
      this.isLogin()
    }
}
</script>

<style>
.header{
  
  margin-left: 4%;
  margin-right: 4%;
  margin-bottom:20px;
  box-shadow: 0px 1px rgb(197, 195, 195);
}

.headerTitle{
  margin-top: 24px;
  margin-left: 30px;
  font-size: 24px;
  font-weight: bold;
  float:left;
}
.headerTitleActive{
  margin-top: 24px;
  margin-left: 30px;
  font-size: 24px;
  font-weight: bold;
  float:left;
  color: rgba(77, 185, 13, 1);
}
.headerTitle:hover{
    color: rgba(77, 185, 13, 1);
}
.headerAvatar{
  margin-top: 20px;
  float:right;
  margin-left:20px
}
.mailLength{
  background-color: rgba(217, 0, 27, 1);
  height: 20px;
  width:20px;
  border-radius: 100%;
  float:right;
  font-size: 10px;
  text-align: center;
  color: white;
  margin-top: 28px;
  margin-left: -15px;
}
.headerBell{
  height: 30px;
  width:30px;
  margin-top: 30px;
  float:right;
  margin-left:20px
}

.headerShpping{
  height: 35px;
  width:35px;
  margin-top: 30px;
  float:right;
}
a:-webkit-any-link {
    color: black;
    text-decoration:none
}
</style>