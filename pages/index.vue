<template>
    <div class="bg-black h-screen overflow-scroll" ref="container">
        <svg viewBox="0 0 500 229" xmlns="http://www.w3.org/2000/svg" ref="svg">
            <defs></defs>
            <path ref="seven" style="transform-origin: 230.991px 164.816px; fill: none; stroke: rgb(235, 235, 233);"
                d="M 424.629 274.149 C 444.876 108.615 287.506 -15.148 141.366 51.376 C 58.885 88.921 8.068 177.834 15.624 271.39"
                transform="matrix(0.999998, 0.001774, -0.001774, 0.999998, 0, 0)" class="seven" />
            <path ref="six" style="transform-origin: 224.595px 177.544px; fill: none; stroke: rgb(237, 235, 235);"
                d="M 149.302 355.614 C 295.987 355.614 387.666 207.226 314.324 88.515 C 272.928 21.517 189.974 -12.841 109.052 3.501"
                transform="matrix(0.115573, -0.993299, 0.993299, 0.115573, 0.000022, -0.000004)" class="six" />
            <path ref="five" style="transform-origin: 222.601px 190.893px; fill: none; stroke: rgb(241, 241, 241);"
                d="M 358.684 274.575 C 374.142 147.758 263.792 53.008 160.054 104.026 C 101.504 132.821 64.976 200.951 69.667 272.62"
                class="five" />
            <path ref="four" style="transform-origin: 222.608px 205.127px; fill: none; stroke: rgb(234, 233, 233);"
                d="M 169.692 320.712 C 272.784 320.712 337.216 224.394 285.671 147.341 C 256.576 103.85 198.276 81.548 141.406 92.156"
                transform="matrix(0.115573, -0.993299, 0.993299, 0.115573, 0.000006, 0.000007)" class="four" />
            <path ref="three" style="transform-origin: 220.543px 219.734px; fill: none; stroke: rgb(235, 234, 234);"
                d="M 177.647 310.704 C 261.22 310.704 313.456 234.899 271.67 174.257 C 248.082 140.032 200.821 122.478 154.717 130.827"
                transform="matrix(0.121464, -0.992596, 0.992596, 0.121464, -0.000013, 0.000008)" class="three" />
            <path ref="two" style="transform-origin: 217.233px 232.864px; fill: none; stroke: rgb(243, 240, 240);"
                d="M 186.099 299.216 C 246.757 299.216 284.669 243.926 254.34 199.692 C 237.219 174.729 202.919 161.926 169.456 168.016"
                transform="matrix(0.115573, -0.993299, 0.993299, 0.115573, 0.000016, 0.000004)" class="two" />
            <path ref="one" style="transform-origin: 216.619px 249.749px; fill: none; stroke: rgb(241, 239, 239);"
                d="M 198.04 289.164 C 234.242 289.164 256.87 256.320 238.768 230.044 C 228.549 215.215 208.078 207.608 188.106 211.228"
                transform="matrix(0.115573, -0.993299, 0.993299, 0.115573, -0.000007, 0.000014)" class="one" />
        </svg>
    </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const container = ref(null)
const svg = ref(null)
const orbits = ['one', 'two', 'three', 'four', 'five', 'six', 'seven']
const orbitRefs = orbits.reduce((acc, orbit) => ({ ...acc, [orbit]: ref(null) }), {})

let tl

const initializeAnimation = () => {
    tl = gsap.timeline({
        scrollTrigger: {
            trigger: container.value,
            start: 'top top',
            end: 'bottom+=100% top',
            scrub: true,
            markers: false,
        },
    })

    orbits.forEach((orbit, index) => {
        const startY = 100
        const endY = 0
        const duration = 1 / orbits.length

        // Make each orbit appear as you scroll down, but keep them on screen
        tl.fromTo(
            orbitRefs[orbit].value,
            {
                y: startY,
                opacity: 0,
                scale: 0.5,
            },
            {
                y: endY,
                opacity: 1,
                scale: 1,
                duration: duration,
                ease: 'power1.inOut',
            },
            index * duration // Make each orbit appear at a staggered time
        )
    })
}

onMounted(() => {
    if (typeof window !== 'undefined') {
        initializeAnimation()
    }
})

onUnmounted(() => {
    if (tl) {
        tl.kill()
    }
})
</script>

<style scoped>
/* Add any styles related to the component here */
</style>