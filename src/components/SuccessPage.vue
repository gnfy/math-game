<template>
    <div class="container">
        <div class="score-box">分数: <span class="score">{{ score }}</span></div>
    </div>
    <div class="restart" @click="restart">
        再玩一次
    </div>
</template>

<script setup>
import { defineEmits, onMounted } from 'vue';
import successAudioSource from '../assets/success.mp3';
const emit = defineEmits(['onChangeStatus']);
const successAudio = new Audio(successAudioSource);
const restart = () => {
    emit('onChangeStatus', 'start');
};
// 分数的key
const scoreKey = 'scorekey';
let score = localStorage.getItem(scoreKey) ?? 0;
onMounted(() => {
    successAudio.play();
});
</script>

<style scoped>
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
}

.score-box {
    text-align: center;
    font-weight: bold;
    font-size: 32px;
}
.score {
    color:#f00;
}

.container img {
    width: 100%;
    display: block;
}


.restart {
    width: 80%;
    height: 100px;
    margin: 40px auto;
    border-radius: 40px;
    background-color: #ffe2a6;
    display: flex;
    color: orange;
    justify-content: center;
    align-items: center;
    font-size: 32px;
    font-weight: bold;
    cursor: pointer;

}
</style>