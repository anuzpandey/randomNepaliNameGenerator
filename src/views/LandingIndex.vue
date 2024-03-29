<script lang="ts" setup>
import axios from "axios"
import {reactive, ref} from "vue"
import confetti from "canvas-confetti"

import BottomBgElement from "../components/Abstract/BottomBgElement.vue"
import SolidButton from "../components/FormElements/SolidButton.vue"

const names = reactive({
  male: [],
  female: [],
})

const randomName = ref(null)

const getRandomName = async (gender = "male") => {
  if (names[gender].length) {
    setAndPopulateRandomName(gender)
    return
  }

  await axios.get(`https://cdn.jsdelivr.net/gh/techgaun/nepali-names/${gender}.txt`)
    .then(response => {
      names[gender] = response.data?.split(/[\n\r]+/)

      setAndPopulateRandomName(gender)
    })
    .catch(error => {
      console.log(error.response)
    })
}

const setAndPopulateRandomName = (gender = "male") => {
  const randomIndex = Math.floor(
    Math.random() * names[gender].length
  )

  randomName.value = names[gender][randomIndex]

  confetti({
    particleCount: 100,
    spread: 70,
    origin: {y: 0.8},
  })
}
</script>

<template>
  <div class="relative px-6 lg:px-8">
    <div class="mx-auto max-w-3xl py-32 sm:py-48 lg:py-56">
      <div class="hidden sm:mb-8 sm:flex sm:justify-center">
        <div class="relative rounded-full py-1 px-3 text-sm leading-6 text-gray-600 ring-1 ring-gray-900/10 hover:ring-gray-900/20">
          Nepali Baby Name generator from Parent's Name
          <a class="font-semibold text-indigo-600 ml-2" href="#">
            <span aria-hidden="true" class="absolute inset-0"></span>
            Coming Soon
            <span aria-hidden="true">&rarr;</span>
          </a>
        </div>
      </div>
      <div class="text-center">
        <h1 class="text-4xl font-bold tracking-tight text-gray-900 sm:text-6xl">Random Nepali Names</h1>

        <div class="mt-8">
          <div class="w-full max-w-2xl mx-auto border-2 border-dashed rounded-lg h-32 p-8 flex items-center justify-center">
            <span id="name-placeholder" class="text-5xl">
              <span>{{ randomName ?? "Random Name" }}</span>
            </span>
          </div>
        </div>

        <div class="mt-8 flex flex-col md:flex-row items-center justify-center gap-y-3 gap-x-6">
          <SolidButton title="Generate Male Name" @click="getRandomName('male')"/>
          <SolidButton button-type="secondary" title="Generate Female Name" @click="getRandomName('female')"/>
        </div>
      </div>
    </div>

    <BottomBgElement/>

  </div>
</template>
