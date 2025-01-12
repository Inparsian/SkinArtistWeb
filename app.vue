<script setup>
    useHead({
        title: "SkinArtistWeb"
    });
    
    import { createCanvas, loadImage } from 'canvas';
    import JSZip from 'jszip';

    const Files = {
        ART: 'art',
        BASE_SKIN: 'baseSkin',
    };

    const art = ref(undefined),
        artImage = ref(undefined),
        baseSkin = ref(undefined),
        baseSkinHeight = ref(32),
        baseSkinImage = ref(undefined);

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
                        baseSkinHeight.value = image.height;
                        baseSkinImage.value = e.target.result;
                    }
                }
            };

            image.src = e.target.result;
        };

        reader.readAsDataURL(file);
    };

    const generate = async () => {
        const dataURIs = [];

        // Loop backwards, skins are sorted from most to least recent
        let i = 1;
        for (let y = 2; y >= 0; y--) {
            for (let x = 8; x >= 0; x--) {
                const canvas = createCanvas(64, baseSkinHeight.value),
                    ctx = canvas.getContext('2d');

                const baseSkin = await loadImage(baseSkinImage.value || useDefaultBaseSkin());
                ctx.drawImage(baseSkin, 0, 0, 64, baseSkinHeight.value);

                // Make sure nothing's on the face second layer
                const faceSecondLayerData = ctx.getImageData(40, 8, 8, 8).data;
                for (let i = 0; i < faceSecondLayerData.length; i += 4) {
                    if (faceSecondLayerData.slice(i, i + 3).some((color) => color !== 0)) {
                        return alert('The face second layer is not empty. Aborting.');
                    }
                }

                const template = await loadImage(artImage.value);
                ctx.drawImage(template, 8 * x, 8 * y, 8, 8, 8, 8, 8, 8);

                // Generate random noise for the skin to be unique, NameMC doesn't like duplicate skins
                const makeNoise = (width, height) => {
                    const noise = ctx.createImageData(width, height);
                
                    for (let i = 0; i < noise.data.length; i += 4) {
                        [ ...Array(3) ].forEach((_, j) => noise.data[i + j] = Math.floor(Math.random() * 256));
                    
                        noise.data[i + 3] = 255;
                    }
                
                    return noise;
                }
            
                ctx.putImageData(makeNoise(2, 2), 19, 3);

                // Image is done, append it to the dataURIs array
                const image = canvas.toDataURL('image/png');
                dataURIs.push(image);
            }
        }

        // Compress all the images into a .zip and download it
        const zip = new JSZip();
        dataURIs.forEach((dataURI, i) => zip.file(`skin${i + 1}.png`, dataURI.split('base64,')[1], { base64: true }));

        zip.generateAsync({ type: 'blob' }).then((blob) => {
            const url = URL.createObjectURL(blob),
                a = document.createElement('a');

            a.href = url;
            a.download = 'skins.zip';
            a.click();
        });
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
                    <input class="file-select bg-stone-600 p-2 rounded-md w-full" type="file" @change="onFileChange($event.target.files[0], Files.ART)">
                    <img v-if="artImage" :src="artImage" class="w-[216px] pixelated" />
                </div>

                <div class="flex flex-col bg-stone-700 rounded-lg p-4 items-center gap-2">
                    <h2 class="text-lg font-bold">Base Skin <span class="font-normal text-stone-500">(Optional)</span></h2>
                    <input class="file-select bg-stone-600 p-2 rounded-md w-full" type="file" @change="onFileChange($event.target.files[0], Files.BASE_SKIN)">
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

            <a href="https://github.com/Inparsian/SkinArtistWeb" class="mt-4 text-xs sm:text-sm font-light underline transition-colors text-stone-300 hover:text-stone-100">View on GitHub</a>
        </div>
    </div>
</template>