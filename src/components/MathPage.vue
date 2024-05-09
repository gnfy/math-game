<template>
    <div class="score-box">分数: <span class="score">{{ score }}</span></div>
    <div class="progress-box">进度: <span class="progress">{{ playTimes }} / {{maxTimes}}</span></div>
    <div class="math">
        <div class="box">{{ current.firstNumber }}</div>
        <div class="box operator">{{ current.operator }}</div>
        <div class="box">{{ current.secondNumber }}</div>
        <div class="box">=</div>
        <div class="box">?</div>
    </div>
    <div class="tooptip-box">
        <div class="tooptip" v-if="toolTip === 'right'">&#128512;</div>
        <div class="tooptip" v-if="toolTip === 'wrong'">&#128530;</div>
    </div>
    <div class="answer">
        <div class="answer-button" @click="answerClick(i)" v-for="i in 10">
            {{ i }}</div>
    </div>
    <!-- <div class="tooptip" v-if="toolTip === 'right'">
        <img src="../assets/right.png" alt="right" />
    </div>
    <div class="tooptip" v-if="toolTip === 'wrong'">
        <img src="../assets/wrong.png" alt="wrong" />
    </div> -->

</template>

<script setup>
import { ref, onMounted, defineEmits } from 'vue'
import rightAudioSource from '../assets/right.mp3'
import wrongAudioSource from '../assets/wrong.mp3'
const emit = defineEmits(['onChangeStatus'])
const mathList = []
const current = ref({})
const toolTip = ref(null)
// 分数
let score = 0
// 分数的key
const scoreKey = 'scorekey';
// 总次数
const maxTimes = 100
// 当前次数
let playTimes = 0
// 当前次数的key
const playTimesKey = 'playtimeskey';

const rightAudio = new Audio(rightAudioSource)
const wrongAudio = new Audio(wrongAudioSource)


function changeToolTip(value) {
    let audio
    toolTip.value = value
    if (value === 'right') {
        score = Number(score) + 1;
        audio = rightAudio
    } else if (value === 'wrong') {
        // score = Number(score) - 1;
        audio = wrongAudio
    }
    // 记录分数
    setScore()
    audio.play()

    setTimeout(() => {
        toolTip.value = null
    }, 3000)
}

// 记录分数
function setScore() {
    localStorage.setItem(scoreKey, score)
}


function createMath() {
    //  生成2-10的随机数
    let result = Math.floor(Math.random() * 8) + 2
    const operator = Math.random() > 0.5 ? '+' : '-'; // 生成规则
    const firstNumber = Math.floor(Math.random() * (result - 1)) + 1
    let secondNumber = result - firstNumber
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

function answerClick(answer) {
    // 执行玩的次数的变更
    updatePlayTimes();
    if (answer === current.value.result) {
        changeToolTip('right')
    } else {
        changeToolTip('wrong')
    }
    if (mathList.length === 0) {
        // 清空一下玩的次数
        localStorage.removeItem(playTimesKey);
        emit('onChangeStatus', 'success')
        return
    }
    current.value = mathList.shift()

}

// 玩的次数变更
function updatePlayTimes() {
    playTimes = 1 + Number(playTimes);
    localStorage.setItem(playTimesKey, playTimes)
}


onMounted(() => {
    // 获得玩的次数
    playTimes = localStorage.getItem(playTimesKey) ?? 0;
    // 一共玩100次，考虑让小朋友分多次玩
    let times = Number(maxTimes) - Number(playTimes);
    for (let i = 0; i < times; i++) {
        mathList.push(createMath())
    }
    // 获得当前的分数
    score = localStorage.getItem(scoreKey) ?? 0;
    // 兼容任意的退出
    if (times === Number(maxTimes)) {
        score = 0;
        setScore();
    }

    current.value = mathList.shift()
})


</script>

<style scoped>
.score-box, .progress-box {
    text-align: center;
    font-weight: bold;
    font-size: 32px;
}
.progress-box {
    font-size: 20px;
}
.score, .progress {
    color:#f00;
}
.math {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 32px;
    font-weight: bold;
    gap: 10px;
    padding: 100px 0 0 0;
    font-weight: bold;


}

.box {
    width: 60px;
    border-radius: 10px;
    background: #ffe2a6;
    line-height: 60px;
    text-align: center;
    color: #8ec1db;
}

.operator {
    color:#f00;
}


.answer {
    padding: 0 20px;
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 10px;
}

.answer-button {
    width: 60px;
    height: 60px;
    border-radius: 10px;
    background: #fff;
    line-height: 60px;
    text-align: center;
    color: #80ccc6;
    font-size: 24px;
    font-weight: bold;
    cursor: pointer;
    box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.25);

}

.tooptip-box {
    height:105px;
}

.tooptip {
    text-align: center;
    font-size: 4rem;
}

/* .tooptip {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}

.tooptip img {
    width: 200px;
    height: 200px;
} */
</style>