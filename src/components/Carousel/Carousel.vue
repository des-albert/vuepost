<template>
  <div class="carousel">
    <div class="columns is-vcentered">
      <div class="column is-1 has-text-centered">
        <button @click="prevSlide" class="button is-info">
          <i class="fas fa-chevron-left"></i>
        </button>
      </div>
      <div class="column is-10">
        <slot :currentSlide="currentSlide" />
      </div>
      <div class="column is-1 has-text-centered">
        <button @click="nextSlide" class="button is-info">
          <i class="fas fa-chevron-right"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { watch, ref, reactive } from "vue"
import { storeToRefs } from "pinia"
import { useStorePosts } from "@/stores/storePosts"

const props = defineProps({
  timeout: {},
})

const storePosts = useStorePosts()
const currentSlide = ref(1)

const { getMode, getSpeed } = storeToRefs(storePosts)

let select = "sequence"
let rate = "medium"
let timeoutDuration = props.timeout

let intID = 0

watch(getMode, (newState) => {
  select = newState
})


watch(getSpeed, (newSpeed) => {
  rate = newSpeed

  if (rate === "slow") timeoutDuration = 10000
  else if (rate === "medium") timeoutDuration = 5000
  else timeoutDuration = 1000

  clearInterval(intID)
  autoPlay()


})

const nextSlide = () => {
  if (currentSlide.value === storePosts.getPostCount) {
    currentSlide.value = 1
    return
  }
  currentSlide.value += 1
}

const prevSlide = () => {
  if (currentSlide.value === 1) {
    currentSlide.value = storePosts.getPostCount
    return
  }
  currentSlide.value -= 1
}

const randomSlide = () => {
  currentSlide.value = Math.floor(Math.random() * storePosts.getPostCount + 1)
}

const autoPlay = () => {

  intID = setInterval(() => {
    if (select === "random") randomSlide()
    else if (select === "sequence") nextSlide()
    else return
  }, timeoutDuration)

}

autoPlay()
</script>
