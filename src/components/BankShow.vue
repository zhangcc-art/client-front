<!-- 银行显示组件，点击查看详细介绍 -->
<template>
    <div class="bankShowCard" @click="chooseBank">
        <el-card :body-style="{ padding: '0px' }" shadow="hover">
            <img :src='bankImgPath' class="bankImg" />
        </el-card>
    </div>
</template>

<script setup>

const emit = defineEmits(['getChoosedBank'])
const prop = defineProps([
    'bankImgPath',
    'bank_id',
])

const chooseBank = () => {
    console.log("[BankShow]点击Bank: ",prop.bank_id)
    emit('getChoosedBank', prop.bank_id)
}

</script>

<style scoped>
.bankShowCard {
    margin: 10px;
}
.bankImg {
    object-fit:fill;
    width: 140px;
    height: 140px;
}
</style>