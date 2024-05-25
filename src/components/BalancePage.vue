<template>
    <div class="title">找平衡</div>
    <div class="balance">
        <div class="scale" :class="scaleClass">
            <div class="left">{{ current.firstNumber }} {{ current.operator }} {{ current.secondNumber }}</div>
            <div class="right">{{ answer }}</div>
        </div>
        <div class="triangle"></div>
    </div>
    <div class="answer" :class="answerStatus">
        <div class="answer-button" @click="answerClick(i)" v-for="i in 10">
            {{ i }}</div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import okAudioSource from '../assets/ok.mp3'
import bigAudioSource from '../assets/big.mp3'
import smallAudioSource from '../assets/small.mp3'
let answer = ref(null)
let scaleClass = ref(null)
let answerStatus = ref(null)
const current = ref({})
const toolTip = ref(null)

const okAudio = new Audio(okAudioSource)
const bigAudio = new Audio(bigAudioSource)
const smallAudio = new Audio(smallAudioSource)


function changeToolTip(value) {
    let audio
    toolTip.value = value
    if (value === 0) {
        scaleClass.value = ''
        audio = okAudio
    } else {
        if (value > 0) {
            scaleClass.value = 'scale-left'
            audio = smallAudio
        } else {
            scaleClass.value = 'scale-right'
            audio = bigAudio
        }
    }
    audio.play()

    setTimeout(() => {
        toolTip.value = ''
        answerStatus.value = ''
        if (value === 0) {
            current.value = createMath()
        }
    }, 2000)
}

function createMath() {
    //  生成2-10的随机数
    let result = Math.floor(Math.random() * 8) + 2
    const operator = Math.random() > 0.5 ? '+' : '-'; // 生成规则
    const firstNumber = Math.floor(Math.random() * (result - 1)) + 1
    let secondNumber = result - firstNumber
    toolTip.value = ''
    answer.value = '?'
    scaleClass.value = 'scale-left'
    if (operator === '+') {
        return {
            firstNumber,
            secondNumber,
            operator,
            result
        }
    } else {
        return {
            firstNumber: result,
            secondNumber: firstNumber,
            operator,
            result: secondNumber
        }

    }
}

function answerClick(val) {
    if (toolTip.value === '') {
        answerStatus.value = 'answer-disabled'
        answer.value = val
        changeToolTip(current.value.result - val)
    }
}

onMounted(() => {
    current.value = createMath()
})
</script>

<style scoped>
.title {
    text-align: center;
    font-size:24px;
    font-weight: bold;
    color: #fff;
}
.answer {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    width: 300px;
    margin: 20px auto;
}

.answer-button {
    width: 90px;
    height: 90px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #fff;
    line-height: 60px;
    text-align: center;
    color: #80ccc6;
    font-size: 24px;
    font-weight: bold;
    cursor: pointer;
    box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.25);
}

.answer-disabled .answer-button {
    background: #ccc;
    cursor: not-allowed;
}

.tooptip-box {
    height:105px;
}

.tooptip {
    text-align: center;
    font-size: 4rem;
}

.balance {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 200px;
  }
  .balance .scale {
    position: relative;
    width: 280px;
    height: 10px;
    background-color: #ffe2a6;
    transition: transform .5s;
  }
  .balance .scale-left {
    transform:rotate(-5deg)
  }
  .balance .scale-right {
    transform:rotate(5deg)
  }
  .balance .left, .balance .right {
    position: absolute;
    width: 90px;
    height: 90px;
    border-radius: 50%;
    background-color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    top: -45px;
    color:#80ccc6;
    font-weight: bold;
  }
  .balance .left { left: -45px; }
  .balance .right { right: -45px; }
  .triangle {
    width: 0;
    height: 0;
    border-left: 20px solid transparent; /* 左边框 */
    border-right: 20px solid transparent; /* 右边框 */
    border-bottom: 34.64px solid #ffe2a6; /* 下边框，34.64px是根号3乘以50，使三角形为等边三角形 */
    position: absolute;
    top:145px;
  }

</style>