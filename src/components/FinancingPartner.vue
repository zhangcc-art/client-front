<template>
    <!-- 这里可以用element卡片组件 -->
    
    <el-card class="financingPartnerCard" :style="cardStyle" shadow="hover" :body-style="{ padding: '5px' }" @click="choosePartner">
        <div >
            <div class="partnerDescription">
                <el-popover
                    placement="top-start"
                    :title='address'
                    :width="200"
                    trigger="hover"
                    :content='description'
                  ><template #reference>
                <img :src='path' class="partnerHead" />
                </template>
                </el-popover>
                <div class="partnerDescriptionLabel">
                    <div class="lable">电话</div>
                    <div class="lable">住址</div>
                    <div class="lable">农作物</div>
                    <div class="lable">金额</div>
                    <div class="lable">面积</div>
                </div>
                <div class="partnerDescriptionInfo">
                    <div>{{ phone }}</div>
                    <span class="addresslayout">{{ address }}</span>
                    <div>{{ crops }}</div>
                    <div>{{ amount }}</div>
                    <div>{{ area }}</div>
                </div>
            </div>
            <div class="partnerV">
                <div class="partnerName">{{ name }}</div>
                <div class="partnerState">{{ choosestate }}</div>
            </div>
            
        </div>
    </el-card>

</template>

<script setup>
import { ref } from 'vue'

const cardStyle = ref()
const ischoose = ref(false)
const choosestate = ref()

const emit = defineEmits(['getChoosedPtn'])
const prop = defineProps([
    'userId',
    'path',
    'name',
    'phone',
    'address',
    'crops',
    'amount',
    'area',
    'description'
])

const choosePartner = () => {
    console.log("[FinancingPartner]点击PartnerBox: ", prop.userId)
    ischoose.value = !(ischoose.value)
    console.log('[FinancingPartner]ischoose: ', ischoose.value)
    emit('getChoosedPtn', ischoose, prop.userId)
    if(ischoose.value) {
        console.log("[FinancingPartner]已选择", prop.userId);
        cardStyle.value = {
            backgroundColor: 'palegreen'
        }
        choosestate.value = '已选择'
    }
    else {
        console.log("[FinancingPartner]未选择");
        cardStyle.value = {
            backgroundColor: 'rgb(247, 255, 253)'
        }
        choosestate.value = ''
    }
}
</script>

<style scoped>
.financingPartnerCard {
    height: 150px;
    width: 350px;
    margin: 10px;
    background-color: rgb(247, 255, 253);
}
.partnerHead {
    height: 100px;
    width: 100px;
    object-fit:fill;
}
.partnerDescription {
    display: flex;
}
.partnerDescriptionLabel{
    width: 50px;
    margin-left: 10px;
}
.lable{
    float: right;
    font-size: 15px;
    color: rgb(94, 94, 94);
}
.partnerDescriptionInfo {
    margin-left: 10px;
    font-size: 15px;
}
.partnerV {
    display: flex;
}
.partnerState {
    width:100px;
    margin-top: 10px;
    text-align: center;
    color:darkseagreen;
    font-size: 20px;
    font-weight:bold;
    font-family:"微软雅黑";
}
.partnerName {
    width:100px;
    margin-top: 10px;
    text-align: center;
    font-size: 20px;
    font-weight:bold;
    font-family:"微软雅黑";
}
.addresslayout {
    display: inline-block;
  white-space: nowrap; 
  width: 150px; 
  overflow: hidden;
  text-overflow:ellipsis;
}
</style>