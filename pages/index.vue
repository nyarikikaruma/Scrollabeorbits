<template>
    <div class="relative w-full h-screen overflow-hidden bg-black" @wheel="handleScroll" ref="container">
        <div class="absolute inset-x-0 bottom-0 flex justify-center">
            <div v-for="(orbit, index) in visibleOrbits" :key="orbit.id"
                class="absolute border-t border-gray-700 rounded-t-full overflow-visible transition-all duration-300"
                :style="getOrbitStyle(index)" :ref="el => { if (el) orbitRefs[index] = el }">

                <!-- Title on the outermost orbit -->
                <div v-if="index === visibleOrbits.length - 1"
                    class="absolute text-white rounded-full px-3 py-1 text-sm font-bold"
                    :style="getTitlePosition(index)">
                    {{ orbit.title }}
                </div>
            </div>
        </div>
        <!-- Profile images container -->
        <div ref="profilesContainer"></div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted, watch, nextTick } from 'vue'

const container = ref(null)
const profilesContainer = ref(null)
const orbitRefs = ref([])

// Current orbit index to track scroll
const currentOrbitIndex = ref(0)

// Generate random profiles for each orbit
const generateProfiles = (count) => {
    return Array.from({ length: count }, (_, i) => ({
        id: i,
        image: `https://picsum.photos/seed/${Math.random()}/40/40`
    }))
}

// Orbit data with titles and profiles
const allOrbits = ref([
    { id: 1, title: 'Today', profiles: generateProfiles(5) },
    { id: 2, title: 'Yesterday', profiles: generateProfiles(6) },
    { id: 3, title: 'Last Week', profiles: generateProfiles(7) },
    { id: 4, title: '2 Weeks Ago', profiles: generateProfiles(8) },
    { id: 5, title: 'Last Month', profiles: generateProfiles(9) },
    { id: 6, title: '2 Months Ago', profiles: generateProfiles(10) },
    { id: 7, title: '3 Months Ago', profiles: generateProfiles(11) },
    { id: 8, title: '6 Months Ago', profiles: generateProfiles(12) },
    { id: 9, title: '1 Year Ago', profiles: generateProfiles(13) },
])

// Visible orbits to show on the screen (7 at a time)
const visibleOrbits = computed(() => {
    const start = currentOrbitIndex.value
    return allOrbits.value.slice(start, start + 7)
})

// Handle scrolling to navigate through orbits
const handleScroll = (event) => {
    if (event.deltaY > 0 && currentOrbitIndex.value < allOrbits.value.length - 7) {
        currentOrbitIndex.value++
    } else if (event.deltaY < 0 && currentOrbitIndex.value > 0) {
        currentOrbitIndex.value--
    }
}

// Get the size and position of each orbit
const getOrbitStyle = (index) => {
    const size = (index + 1) * 320 // Orbit size grows with index
    return {
        width: `${size}px`,
        height: `${size / 2}px`, // Half circle
        bottom: `-${size / 4}px`,
    }
}

// Title position on the outermost orbit
const getTitlePosition = (index) => {
    const size = (index + 1) * 320
    return {
        top: `-${size / 2 + 20}px`, // Move title above the outer edge of the orbit
        left: '50%',
        transform: 'translateX(-50%)',
    }
}

// Function to position profile images
const positionProfileImages = () => {
    if (!profilesContainer.value) return

    // Clear existing profile images
    profilesContainer.value.innerHTML = ''

    visibleOrbits.value.forEach((orbit, orbitIndex) => {
        const orbitElement = orbitRefs.value[orbitIndex]
        if (!orbitElement) return

        const orbitRect = orbitElement.getBoundingClientRect()
        const centerX = orbitRect.left + orbitRect.width / 2
        const centerY = orbitRect.top + orbitRect.height

        orbit.profiles.forEach((profile, profileIndex) => {
            const angle = (profileIndex / (orbit.profiles.length - 1)) * Math.PI
            const radius = orbitRect.width / 2

            const x = centerX + Math.cos(angle) * radius
            const y = centerY - Math.sin(angle) * radius

            const img = document.createElement('img')
            img.src = profile.image
            img.alt = 'Profile'
            img.className = 'absolute w-8 h-8 rounded-full object-cover'
            img.style.left = `${x}px`
            img.style.top = `${y}px`
            img.style.transform = 'translate(-50%, -50%)'

            profilesContainer.value.appendChild(img)
        })
    })
}

// Watch for changes in visibleOrbits and reposition images
watch(visibleOrbits, () => {
    nextTick(positionProfileImages)
})

onMounted(() => {
    nextTick(positionProfileImages)
    window.addEventListener('resize', positionProfileImages)
})

// Clean up event listener on component unmount
onUnmounted(() => {
    window.removeEventListener('resize', positionProfileImages)
})
</script>

<style scoped>
.orbit {
    position: relative;
    overflow: hidden;
}
</style>