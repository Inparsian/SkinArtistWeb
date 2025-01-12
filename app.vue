<script setup>
    import { createCanvas, loadImage } from 'canvas';
    import JSZip from 'jszip';

    const Files = {
        ART: 'art',
        BASE_SKIN: 'baseSkin',
    };

    const defaultBaseSkin = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEAAAAAgCAYAAACinX6EAAAGAmVYSWZJSSoACAAAAAoAAAEEAAEAAABAAAAAAQEEAAEAAAAgAAAAAgEDAAMAAACGAAAAEgEDAAEAAAABAAAAGgEFAAEAAACMAAAAGwEFAAEAAACUAAAAKAEDAAEAAAADAAAAMQECAA0AAACcAAAAMgECABQAAACqAAAAaYcEAAEAAAC+AAAA0AAAAAgACAAIADcCAAAUAAAANwIAABQAAABHSU1QIDIuMTAuMzYAADIwMjQ6MDU6MDIgMTk6MjI6NDcAAQABoAMAAQAAAAEAAAAAAAAACQD+AAQAAQAAAAEAAAAAAQQAAQAAAAABAAABAQQAAQAAAIAAAAACAQMAAwAAAEIBAAADAQMAAQAAAAYAAAAGAQMAAQAAAAYAAAAVAQMAAQAAAAMAAAABAgQAAQAAAEgBAAACAgQAAQAAALkEAAAAAAAACAAIAAgA/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCACAAQADASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwD5/ooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKK6fxf4Q/4RT7H/p32r7Tv/wCWOzbt2/7Rz979K5ipjJSV0OUXF2YUUUVQgooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKKKKACiiigAooooAKK6nxl4y/4S37F/oH2T7L5n/LbzN27b/sjGNv61y1JNtajaSegUUUUxBRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAFFFFABRRRQAUUUUAf//ZAISAtwcAAAGDaUNDUElDQyBwcm9maWxlAAB4nH2RPUjDQBzFX1NLRSoOFhVxyFCd7KIijlqFIlQItUKrDiaXfkGThiTFxVFwLTj4sVh1cHHW1cFVEAQ/QJwdnBRdpMT/JYUWMR4c9+PdvcfdO0BoVJhmdc0Cmm6b6WRCzOZWxfArQhAwiAEEZWYZc5KUgu/4ukeAr3dxnuV/7s/Rq+YtBgRE4llmmDbxBvH0pm1w3ieOspKsEp8Tj5t0QeJHrisev3EuuizwzKiZSc8TR4nFYgcrHcxKpkY8RRxTNZ3yhazHKuctzlqlxlr35C+M5PWVZa7THEESi1iCBBEKaiijAhtxWnVSLKRpP+HjH3b9ErkUcpXByLGAKjTIrh/8D353axUmJ7ykSAIIvTjOxygQ3gWadcf5Pnac5gkQfAau9La/2gBmPkmvt7XYEdC3DVxctzVlD7jcAYaeDNmUXSlIUygUgPcz+qYc0H8L9Kx5vbX2cfoAZKir1A1wcAiMFSl73efd3Z29/Xum1d8PJ0JyiGbbZGAAAA8+aVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8P3hwYWNrZXQgYmVnaW49Iu+7vyIgaWQ9Ilc1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCI/Pgo8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJYTVAgQ29yZSA0LjQuMC1FeGl2MiI+CiA8cmRmOlJERiB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRheC1ucyMiPgogIDxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSIiCiAgICB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIKICAgIHhtbG5zOnN0RXZ0PSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VFdmVudCMiCiAgICB4bWxuczpkYz0iaHR0cDovL3B1cmwub3JnL2RjL2VsZW1lbnRzLzEuMS8iCiAgICB4bWxuczpHSU1QPSJodHRwOi8vd3d3LmdpbXAub3JnL3htcC8iCiAgICB4bWxuczp0aWZmPSJodHRwOi8vbnMuYWRvYmUuY29tL3RpZmYvMS4wLyIKICAgIHhtbG5zOnhtcD0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wLyIKICAgeG1wTU06RG9jdW1lbnRJRD0iZ2ltcDpkb2NpZDpnaW1wOmQ5MGNjOGRkLTVkNzAtNGI4ZC1iNmI3LTE4OTBlNjJjNWI1MyIKICAgeG1wTU06SW5zdGFuY2VJRD0ieG1wLmlpZDpkNTljNGEzOC0wZTM5LTQ3MWEtYWZlMy1jODg1NzU4YjRlMWMiCiAgIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDoxMGNhZDkwNy1lYmJiLTQ4ZjktOTEzZS1jMDVjYjMzNTcxZTYiCiAgIGRjOkZvcm1hdD0iaW1hZ2UvcG5nIgogICBHSU1QOkFQST0iMi4wIgogICBHSU1QOlBsYXRmb3JtPSJMaW51eCIKICAgR0lNUDpUaW1lU3RhbXA9IjE3MTQ2OTIxNjc2ODA5MTAiCiAgIEdJTVA6VmVyc2lvbj0iMi4xMC4zNiIKICAgdGlmZjpPcmllbnRhdGlvbj0iMSIKICAgeG1wOkNyZWF0b3JUb29sPSJHSU1QIDIuMTAiCiAgIHhtcDpNZXRhZGF0YURhdGU9IjIwMjQ6MDU6MDJUMTk6MjI6NDctMDQ6MDAiCiAgIHhtcDpNb2RpZnlEYXRlPSIyMDI0OjA1OjAyVDE5OjIyOjQ3LTA0OjAwIj4KICAgPHhtcE1NOkhpc3Rvcnk+CiAgICA8cmRmOlNlcT4KICAgICA8cmRmOmxpCiAgICAgIHN0RXZ0OmFjdGlvbj0ic2F2ZWQiCiAgICAgIHN0RXZ0OmNoYW5nZWQ9Ii8iCiAgICAgIHN0RXZ0Omluc3RhbmNlSUQ9InhtcC5paWQ6MWQ1MzM4N2EtZWE1MS00N2Y5LWE2ZGItODU5NWMwYTMxZjdmIgogICAgICBzdEV2dDpzb2Z0d2FyZUFnZW50PSJHaW1wIDIuMTAgKExpbnV4KSIKICAgICAgc3RFdnQ6d2hlbj0iMjAyNC0wNS0wMlQwODoyNjo1Mi0wNDowMCIvPgogICAgIDxyZGY6bGkKICAgICAgc3RFdnQ6YWN0aW9uPSJzYXZlZCIKICAgICAgc3RFdnQ6Y2hhbmdlZD0iLyIKICAgICAgc3RFdnQ6aW5zdGFuY2VJRD0ieG1wLmlpZDoxMzdlN2M2OC1jMWE2LTQ0MDEtODQ1OS0zNjBjOTgzOWE3ZGIiCiAgICAgIHN0RXZ0OnNvZnR3YXJlQWdlbnQ9IkdpbXAgMi4xMCAoTGludXgpIgogICAgICBzdEV2dDp3aGVuPSIyMDI0LTA1LTAyVDE4OjUzOjA0LTA0OjAwIi8+CiAgICAgPHJkZjpsaQogICAgICBzdEV2dDphY3Rpb249InNhdmVkIgogICAgICBzdEV2dDpjaGFuZ2VkPSIvIgogICAgICBzdEV2dDppbnN0YW5jZUlEPSJ4bXAuaWlkOjliZjZkNmUwLTcyY2QtNGM3Ny1iYWI0LTg3ZWRlMWU1YjcxYSIKICAgICAgc3RFdnQ6c29mdHdhcmVBZ2VudD0iR2ltcCAyLjEwIChMaW51eCkiCiAgICAgIHN0RXZ0OndoZW49IjIwMjQtMDUtMDJUMTk6MjI6NDctMDQ6MDAiLz4KICAgIDwvcmRmOlNlcT4KICAgPC94bXBNTTpIaXN0b3J5PgogIDwvcmRmOkRlc2NyaXB0aW9uPgogPC9yZGY6UkRGPgo8L3g6eG1wbWV0YT4KICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgIAo8P3hwYWNrZXQgZW5kPSJ3Ij8+cDFIGwAAAAZiS0dEAAAA/wAAR9uPkgAAAAlwSFlzAAALEwAACxMBAJqcGAAAAAd0SU1FB+gFAhcWL/oKSc8AAAB3SURBVGje7dgxDoAgDAXQ4uzgOT2Fl3D2KB4NZycTIUrS93cS8kIppcRzarSlxMCZInkAAAAAIDfAua9zZoDSoc/32IMSAAAAAAAAAMbowfXFmi//D1r35wTcn8LHtiiBxCUwwizw66zhDgAAAAAAAAAAAACQNBcjVAz0XzeA5wAAAABJRU5ErkJggg==";

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

    const generate = async () => {
        const dataURIs = [];

        // Loop backwards, skins are sorted from most to least recent
        let i = 1;
        for (let y = 2; y >= 0; y--) {
            for (let x = 8; x >= 0; x--) {
                const canvas = createCanvas(64, 32),
                    ctx = canvas.getContext('2d');

                const baseSkin = await loadImage(baseSkinImage.value || defaultBaseSkin);
                ctx.drawImage(baseSkin, 0, 0, 64, 32);

                // Make sure nothing's on the face second layer
                const faceSecondLayerData = ctx.getImageData(40, 8, 8, 8).data;
                for (let i = 0; i < faceSecondLayerData.length; i += 4) {
                    if (faceSecondLayerData.slice(i, i + 3).some((color) => color !== 0)) {
                        return alert('The face second layer is not empty. Aborting.');
                    }
                }

                const template = await loadImage(artImage.value);
                ctx.drawImage(template, 8 * x, 8 * y, 8, 8, 8, 8, 8, 8);

                // Generate random noise for the skin to be unique
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
        </div>
    </div>
</template>