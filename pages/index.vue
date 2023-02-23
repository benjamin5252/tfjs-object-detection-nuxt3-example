<template>
    <div>
        <div v-if="isLoadingModel">Loading Model ...</div>
        <div v-else>
            <label for="avatar">Choose a picture:</label>
            <input @change="getChange($event)" type="file" id="avatar" name="avatar" accept="image/*">
        </div>
        <img id="img_to_detect">
        <canvas id="detect_result"></canvas>

    </div>
</template>

<script setup>
// import modules for object detection
import '@tensorflow/tfjs-backend-cpu'
import '@tensorflow/tfjs-backend-webgl'
import * as cocoSsd from '@tensorflow-models/coco-ssd'

const isLoadingModel = ref(false)
let cocoSsdModel = null
onMounted(async () => {
    isLoadingModel.value = true
    // Load the ai model
    cocoSsdModel = await cocoSsd.load()
    isLoadingModel.value = false
})

const getChange = (e) => {
    const url = URL.createObjectURL(e.target.files[0])
    const img = document.getElementById('img_to_detect')
    img.width = 300
    img.height = 300
    img.src = url
    let result = null;
    img.onload = async () => {
        result = await getDetect(img)
        
    }
}

const getDetect = async (img) => {
    // get detection result
    const result = await cocoSsdModel.detect(img)

    const canvas = document.getElementById('detect_result');
    const color=["red","green","blue"]
    canvas.width=img.width ;
    canvas.height=img.height ;
    const context = canvas.getContext('2d');
    context.drawImage(img, 0, 0, img.width,img.height);
    context.font = '40px Arial';
    
    for (let i = 0; i < result.length; i++) {
        context.beginPath();
        context.rect(...result[i].bbox);
        context.lineWidth = 5;
        context.strokeStyle = color[i%3];
        context.fillStyle = color[i%3];
        context.stroke();
        context.fillText(
            result[i].score.toFixed(3) + ' ' + result[i].class, 
            result[i].bbox[0],
            result[i].bbox[1] - 5);
    }
    return result
}

const drawDetect = ()=>{

}
</script>

<style>

</style>