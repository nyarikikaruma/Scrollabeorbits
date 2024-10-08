<template>
    <div class="relative w-full h-screen bg-black overflow-hidden" @wheel="handleZoom" ref="container">
        <div class="absolute inset-0 flex items-center justify-center">
            <div class="relative" :style="{ transform: `scale(${zoomLevel})` }">

                <!-- Orbits and Avatars -->
                <template v-for="(orbit, orbitIndex) in visibleOrbits" :key="orbitIndex">
                    <div class="absolute rounded-full" :style="{
                        width: `${orbit.radius * 2}px`,
                        height: `${orbit.radius * 2}px`,
                        left: `calc(50% - ${orbit.radius}px)`,
                        top: `calc(50% - ${orbit.radius}px)`,
                        borderWidth: '1px',
                        borderStyle: 'solid',
                        borderColor: 'rgba(255, 255, 255, 0.2)'
                    }">
                    </div>
                    <template v-for="(avatar, avatarIndex) in orbit.avatars" :key="`${orbitIndex}-${avatarIndex}`">
                        <div v-if="isAvatarVisible(avatar)"
                            class="absolute transition-transform duration-300 hover:scale-110" :style="{
                                width: `${avatar.size}px`,
                                height: `${avatar.size}px`,
                                left: `calc(50% + ${orbit.radius * Math.cos(avatar.angle)}px - ${avatar.size / 2}px)`,
                                top: `calc(50% - ${orbit.radius * Math.sin(avatar.angle)}px - ${avatar.size / 2}px)`
                            }" @mouseenter="showAvatarInfo(avatar, $event, orbitIndex, avatarIndex)"
                            @mouseleave="hideAvatarInfo(orbitIndex, avatarIndex)">
                            <!-- Using Lorem Picsum for real profile images -->
                            <img :src="avatar.imageUrl" :alt="avatar.name"
                                class="w-full h-full rounded-full object-cover">

                            <!-- Avatar Info Card (positioned below avatar) -->
                            <div v-if="isAvatarActive(orbitIndex, avatarIndex)"
                                class="absolute bg-gray-800 text-white p-2 rounded shadow-lg text-center" :style="{
                                    top: '100%', /* Immediately below the avatar */
                                    left: '50%',
                                    transform: 'translateX(-50%)'
                                }">
                                <h3 class="font-bold text-sm">{{ avatar.name }}</h3>
                                <p class="text-xs">Orbit: {{ avatar.orbit }}</p>
                                <p class="text-xs">Size: {{ avatar.size }}px</p>
                                <p class="text-xs">Angle: {{ (avatar.angle * 180 / Math.PI).toFixed(2) }}°</p>
                            </div>
                        </div>
                    </template>
                </template>
            </div>
        </div>

        <!-- Zoom Controls -->
        <div class="absolute bottom-4 right-4 flex space-x-2">
            <button @click="zoomIn" class="bg-blue-500 text-white px-4 py-2 rounded">+</button>
            <button @click="zoomOut" class="bg-blue-500 text-white px-4 py-2 rounded">-</button>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            zoomLevel: 0.5,
            orbits: this.generateOrbits(20),  // Generate more orbits (20 in this example)
            activeAvatars: {}, // Store the active avatars for each orbit and avatar
        };
    },
    computed: {
        visibleOrbits() {
            const orbitRange = 7;
            const radiusThreshold = 50; // Distance at which orbits will become visible
            return this.orbits.filter((orbit, index) => {
                const effectiveZoom = this.zoomLevel * orbit.radius;
                return index < orbitRange || effectiveZoom <= radiusThreshold * (orbitRange + 1);
            });
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
        handleZoom(event) {
            if (event.deltaY < 0) {
                this.zoomIn();
            } else {
                this.zoomOut();
            }
        },
        zoomIn() {
            if (this.zoomLevel < 2) {
                this.zoomLevel += 0.1;
            }
        },
        zoomOut() {
            if (this.zoomLevel > 0.2) {
                this.zoomLevel -= 0.1;
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
