<!-- 当前融资申请、历史融资申请的信息卡片 -->
<template>
    <el-card class="financingApplyInfoCard" :body-style="{ padding: '0px' }" @click="clickApplyInfo">
        <div class="financingApplyInfoCardHeader">
            <img
            :src=avatar
            class="image"
            />
            <span class="text">{{bankName}}</span>
        </div>
        <div class="divider"></div>
        <div class="financingApplyInfoCardBody">
            <div class="cardBodyCenter">
                <div class="cardBodyCenter1">
                    <span class="text1">贷款额</span>
                    <span class="text2">{{applyMoney}}</span>
                </div>
                <div class="cardBodyCenter2">
                    <span class="text1">贷款状态</span>
                    <span class="text2">{{state}}</span>
                </div>
            </div>
            <div class="divider"></div>
            <div class="financingApplyInfoCardBottom">
                <span class="cardBottomText">融资发起人</span>
                <span class="cardBottomTime">时间 {{ createTime }}</span>
            
                
            </div>
        </div>
    </el-card>
</template>

<script setup>
import {useRouter} from 'vue-router'
const router = useRouter()

const prop = defineProps([
    'financingId',
    'avatar',
    'bankName',
    'applyMoney',
    'state',
    'createTime',
])

const clickApplyInfo = () => {
    router.push({
        path: '/index/myHome/userFinancingDetail',
        query: {
            financingId: prop.financingId,
        }
    })
}

</script>

<style scoped>
.financingApplyInfoCard {
    padding: 0;
    width: 400px;
}
.financingApplyInfoCardHeader {
    display: flex;
    align-items: center;
    padding: 5px;
}
.image {
    border-radius: 50%;
    width: 50px;
}
.text{
    font-size:30px;
    font-weight:bold;
    font-family:"微软雅黑";
}
.divider {
    margin: 0 auto;
    height: 1px;
    background-color: #d4d4d4;
    font-size: 16px;
    color: rgba(101, 101, 101, 1);
}
.cardBodyCenter {
    display: flex;
    justify-content: center;
    margin-top: 20px;
}
.cardBodyCenter1 {
    margin-right: 20px;
    display: flex;
    flex-direction: column;
}
.cardBodyCenter2 {
    margin-left: 20px;
    display: flex;
    flex-direction: column;
}
.text1 {
    align-self:center;
    font-size:large;
}
.text2 {
    align-self:center;
    margin-top: 20px;
    margin-bottom: 20px;
    font-size: 50px;
    color: red;
    font-weight:bold;
    font-family:"微软雅黑";
}
.financingApplyInfoCardBottom {
    margin: 20px;
}
.cardBottomText {
    
    margin-left: 50px;
    font-size: 16px;
    color: #000000;
}
.cardBottomTime {
    float: right;
    margin-right: 30px;
    font-size: 14px;
    color: #7e7e7e;
}
</style>