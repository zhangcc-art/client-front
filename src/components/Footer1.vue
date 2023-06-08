<template>
    <div style="width: 100%;height: 420px;background-color: #232323;padding-left: 10%;padding-right: 10%;padding-top: 150px;margin-top: 30px;">
        <el-row :gutter="30">
            <el-col :span="6">
                <div style="color: white;font-size: 24px;margin-bottom: 50px;" @click="toTop">-->滑到最上</div>
                <div style="color: white;font-size: 24px;margin-bottom: 50px;" @click="toTop">滑到最上</div>
            </el-col>
            <el-col :span="6">
                <div style="color: white;font-size: 24px;margin-bottom: 20px;">欢迎你加入“融销通”团队</div>
                <img src="https://cdn.pixabay.com/photo/2023/03/03/13/28/bird-7827680__340.jpg" style="height:60px;width:60px;">
                <div style="clear:both"></div>
                <img src="https://cdn.pixabay.com/photo/2023/02/10/07/59/fox-7780326__340.jpg" style="height:60px;width:60px;margin-top: 20px;">
            </el-col>
            <el-col :span="4">
                <div style="color: white;font-size: 24px;margin-bottom: 40px;">链接</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(0,'/index/home')">->首页</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(1,'/index/productSource')">->商品货源</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(2,'/index/productNeeds')">->求购需求</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(3,'/index/knowledge')">->农业知识</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(4,'/index/expert')">->专家指导</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(8,'/index/financing')">->融资</div>
                <div style="color: RGB(221,221,221);font-size: 14px;margin-bottom: 5px;" @click="change(8,'/index/myHome/myInfo')">->个人中心</div>
            </el-col>
            <el-col :span="4">
                <img src="https://cdn.pixabay.com/photo/2023/03/06/21/33/nature-7834580__340.jpg" style="height:80%;width:80%;margin-top: 20px;">
            </el-col>
        </el-row>
    </div>
</template>

<script>
export default {
    name:'Footer',
    data(){
        return{

        }
    },
    methods:{
        change(index,href){
        window.scrollTo(0,0);
        this.$router.push(href)
        this.$store.state.current=index;
	    },
        toTop(){
            window.scrollTo(0,0);
        }
    }
}
</script>

<style>

</style>