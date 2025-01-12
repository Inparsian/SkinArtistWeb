<script setup>
    const Files = {
        ART: 'art',
        BASE_SKIN: 'baseSkin',
    };

    const art = ref(null),
        artImage = ref(null),
        baseSkin = ref(null),
        baseSkinImage = ref(null);

    const onFileChange = (file, type) => {
        const reader = new FileReader();

        reader.onload = (e) => {
            const image = new Image();

            image.onload = () => {
                if (type === Files.ART) {
                    if (image.width !== 72 || image.height !== 24) {
                        alert('Art must be 72x24 pixels');
                    } else {
                        art.value = file;
                        artImage.value = e.target.result;
                    }
                } else if (type === Files.BASE_SKIN) {
                    if (image.width !== 64 || (image.height !== 32 && image.height !== 64)) {
                        alert('Base skin must be 64x32 or 64x64 pixels');
                    } else {
                        baseSkin.value = file;
                        baseSkinImage.value = e.target.result;
                    }
                }
            };

            image.src = e.target.result;
        };

        reader.readAsDataURL(file);
    };

    const generate = () => {
        console.log('Generate');
    };
</script>

<template>
    <div class="bg-stone-900 text-white overflow-hidden w-screen h-screen flex flex-col justify-center items-center">
        <NuxtRouteAnnouncer />
        
        <div class="bg-stone-800 rounded-3xl p-8 flex flex-col gap-2 justify-center items-center">
            <h1 class="text-2xl sm:text-3xl font-black">SkinArtistWeb</h1>
            <p class="text-xs sm:text-sm font-light">Upload an image to get started</p>

            <div class="flex flex-wrap justify-center gap-4 my-4">
                <div class="flex flex-col bg-stone-700 rounded-lg p-4 items-center gap-2">
                    <h2 class="text-lg font-bold">Art</h2>
                    <input class="bg-stone-600 p-2 rounded-md w-full" type="file" @change="onFileChange($event.target.files[0], Files.ART)">
                    <img v-if="artImage" :src="artImage" class="w-[216px] pixelated" />
                </div>

                <div class="flex flex-col bg-stone-700 rounded-lg p-4 items-center gap-2">
                    <h2 class="text-lg font-bold">Base Skin <span class="font-normal text-stone-500">(Optional)</span></h2>
                    <input class="bg-stone-600 p-2 rounded-md w-full" type="file" @change="onFileChange($event.target.files[0], Files.BASE_SKIN)">
                    <img v-if="baseSkinImage" :src="baseSkinImage" class="w-[192px] pixelated" />
                </div>
            </div>

            <button 
                class="transition-colors disabled:cursor-not-allowed disabled:opacity-50 bg-stone-600 enabled:hover:bg-stone-700 enabled:active:bg-stone-500 p-3 rounded-lg" 
                :disabled="!art"
                @click="generate"
            >
                Generate
            </button>
        </div>
    </div>
</template>

<style>
    .pixelated {
        image-rendering: optimizeSpeed;
        image-rendering: -moz-crisp-edges;
        image-rendering: -o-crisp-edges;
        image-rendering: -webkit-optimize-contrast;
        image-rendering: pixelated;
        image-rendering: optimize-contrast;
        -ms-interpolation-mode: nearest-neighbor;
    }
</style>