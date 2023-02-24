# tfjs-object-detection-nuxt3-example


## Setup

Make sure to install the dependencies:

```bash
# yarn
yarn install

# npm
npm install

# pnpm
pnpm install
```

## Development Server

Start the development server on http://localhost:3000

```bash
npm run dev
```

## Production

Build the application for production:

```bash
npm run build
```

Locally preview production build:

```bash
npm run preview
```

Check out the [deployment documentation](https://nuxt.com/docs/getting-started/deployment) for more information.

[Nuxt3 Tutorial] How to perform coco-ssd object detection powered by tensorflow.js in a Nuxt 3 app.

Intro
In this project, a simple example of using the pre-trained object detection model (coco-ssd) powered by tensorflow.js in a Nuxt 3 app is built.

Github Repo of the source code of this project:

https://github.com/benjamin5252/tfjs-object-detection-nuxt3-example

Create Nuxt app
Create one Nuxt 3 app according to the Installation instruction reference down below.

Reference:

Installation
Get started with Nuxt quickly with our online starters or start locally with your terminal. You can start playing with…
nuxt.com

Install dependencies
Reference:

https://github.com/tensorflow/tfjs-models/tree/master/coco-ssd

According to the GitHub repo of the coco-ssd object detection pre-trained model, we have to install the following dependency “@tensorflow/tfjs-backend-cpu”, “@tensorflow/tfjs-backend-webgl”, and “@tensorflow-models/coco-ssd”. I found the latest version of “@tensorflow-models/coco-ssd” is not compatible with the latest version of the other two dependencies. The package.json is listed below. We can edited the package.json like below and perform “npm install”.

package.json
```json
{
  "private": true,
  "scripts": {
    "build": "nuxt build",
    "dev": "nuxt dev",
    "generate": "nuxt generate",
    "preview": "nuxt preview",
    "postinstall": "nuxt prepare"
  },
  "devDependencies": {
    "nuxt": "^3.2.0"
  },
  "dependencies": {
    "@tensorflow-models/coco-ssd": "^2.2.2",
    "@tensorflow/tfjs-backend-cpu": "^3.21.0",
    "@tensorflow/tfjs-backend-webgl": "^3.21.0"
  }
}
```
After editing the package.json perform the following command to install the dependencies

npm install
Replace the <NuxtWelcome /> in “app.vue” with “<NuxtPage />” like below.

<template>
  <div>
    <NuxtPage />
  </div>
</template>
Make a “pages” folder in our project and put “index.vue” for our main page. Then the nuxt app is setup for our project. The following part of the project will performed on this “index.vue” page.


Use tensorflow.js coco-ssd object detection in the Nuxt app
Add some UI to read images and a canvas to show the detection result in index.vue page. (I added the code in my “index.vue” page)

index.vue


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
</script>
<style>
</style>
Import the modules with the code below in the top part of your Vue component <script setup> section.


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

</script>
<style>
</style>
Create some variables for page interaction and data store.

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
</script>
<style>
</style>
Load the ai model with “cocoSsd.load()” in the code beload when the page is mounted.

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
</script>
<style>
</style>
Get detection by passing the “img” element in to the “cocoSsdModel.detect(img)” function to get the detection result. The function to draw the detection result on to the canvas is show as “getDetect()” function below. And a “getChange()” is added to get the file input and pass the img to “getDetect(img)” after the img is loaded. The code is completed in this project down below.

index.vue (complete)


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
</script>

<style>
</style>
Result:
Run the dev comment to show the outcome.

npm run dev
The web page interaction will be like below.


