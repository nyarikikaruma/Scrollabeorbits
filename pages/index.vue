<template>
    <div class="relative w-full h-screen bg-black overflow-hidden" @wheel="handleScroll" ref="container">
        <!-- Central Orbit Section -->
        <div class="absolute inset-0 flex items-center justify-center">
            <div class="relative">
                <!-- Render Orbits -->
                <template v-for="(orbit, orbitIndex) in visibleOrbits" :key="orbitIndex">
                    <div class="absolute rounded-full border border-gray-500 transition-all duration-500 ease-out"
                        :style="{
                            width: `${orbit.radius * 2}px`,
                            height: `${orbit.radius * 2}px`,
                            left: `calc(50% - ${orbit.radius}px)`,
                            top: `calc(50% - ${orbit.radius}px)`
                        }">
                    </div>

                    <!-- Render Avatars in Each Orbit -->
                    <template v-for="(avatar, avatarIndex) in orbit.avatars" :key="`${orbitIndex}-${avatarIndex}`">
                        <div v-if="isAvatarVisible(avatar)"
                            class="absolute transition-transform duration-300 hover:scale-110" :style="{
                                width: `${avatar.size}px`,
                                height: `${avatar.size}px`,
                                left: `calc(50% + ${orbit.radius * Math.cos(avatar.angle)}px - ${avatar.size / 2}px)`,
                                top: `calc(50% - ${orbit.radius * Math.sin(avatar.angle)}px - ${avatar.size / 2}px)`
                            }" @mouseenter="showAvatarInfo(avatar, $event, orbitIndex, avatarIndex)"
                            @mouseleave="hideAvatarInfo(orbitIndex, avatarIndex)">

                            <!-- Avatar Image -->
                            <img :src="avatar.imageUrl" :alt="avatar.name"
                                class="w-full h-full rounded-full object-cover">

                            <!-- Avatar Info Tooltip -->
                            <div v-if="isAvatarActive(orbitIndex, avatarIndex)"
                                class="absolute bg-gray-800 text-white p-2 rounded shadow-lg text-center"
                                :style="{ top: '100%', left: '50%', transform: 'translateX(-50%)' }">
                                <h3 class="font-bold text-sm">{{ avatar.name }}</h3>
                                <p class="text-xs">Orbit: {{ avatar.orbit }}</p>
                            </div>
                        </div>
                    </template>
                </template>
            </div>
        </div>

        <!-- Scroll Controls -->
        <div class="absolute bottom-4 right-4 flex space-x-2">
            <button @click="scrollUp" class="bg-blue-500 text-white px-4 py-2 rounded">↑</button>
            <button @click="scrollDown" class="bg-blue-500 text-white px-4 py-2 rounded">↓</button>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            orbits: this.generateOrbits(20),  // Generate more orbits (20 in this example)
            activeAvatars: {}, // Store the active avatars for each orbit and avatar
            maxVisibleOrbits: 7, // Maximum number of visible orbits
            orbitStartIndex: 0, // Track the starting index for visible orbits
        };
    },
    computed: {
        visibleOrbits() {
            const orbitRange = this.maxVisibleOrbits;
            const orbitCount = this.orbits.length;

            // Calculate the range of visible orbits based on the orbitStartIndex
            const startIndex = Math.max(0, Math.min(orbitCount - orbitRange, this.orbitStartIndex));

            return this.orbits.slice(startIndex, startIndex + orbitRange);
        }
    },
    methods: {
        generateOrbits(count) {
            const baseRadius = 50;
            const radiusIncrement = 40;
            return Array.from({ length: count }, (_, index) => ({
                radius: baseRadius + index * radiusIncrement,
                avatars: this.generateAvatars(10, index + 1, baseRadius + index * radiusIncrement),
            }));
        },
        generateAvatars(count, orbitNumber, orbitRadius) {
            return Array.from({ length: count }, (_, index) => ({
                name: `Avatar ${orbitNumber}-${index + 1}`,
                size: 30,
                angle: (Math.PI * index) / (count - 1),
                imageUrl: `https://picsum.photos/seed/${orbitNumber}-${index}/30`,
                orbit: orbitNumber,
            }));
        },
        isAvatarVisible(avatar) {
            return avatar.angle >= 0 && avatar.angle <= Math.PI;
        },
        handleScroll(event) {
            if (event.deltaY < 0) {
                this.scrollUp();
            } else {
                this.scrollDown();
            }
        },
        scrollUp() {
            // Move up smoothly if not at the topmost orbit
            if (this.orbitStartIndex > 0) {
                this.orbitStartIndex--;
            }
        },
        scrollDown() {
            // Move down smoothly if not at the bottommost orbit
            if (this.orbitStartIndex < this.orbits.length - this.maxVisibleOrbits) {
                this.orbitStartIndex++;
            }
        },
        showAvatarInfo(avatar, event, orbitIndex, avatarIndex) {
            this.$set(this.activeAvatars, `${orbitIndex}-${avatarIndex}`, true);
        },
        hideAvatarInfo(orbitIndex, avatarIndex) {
            this.$delete(this.activeAvatars, `${orbitIndex}-${avatarIndex}`);
        },
        isAvatarActive(orbitIndex, avatarIndex) {
            return !!this.activeAvatars[`${orbitIndex}-${avatarIndex}`];
        }
    }
};
</script>
