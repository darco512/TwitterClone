<template>
    <template v-for="(attachment, ind) of attachments.slice(0, 4)" >
        <div
            @click="$emit('attachmentClick',ind)"
            class="group bg-blue-100 aspect-square flex flex-col items-center justify-center text-gray-500 relative mb-3"
        >

            <div v-if="ind===3 && attachments.length > 4" class="absolute left-0 top-0 right-0 bottom-0 z-10 bg-black/60 text-white flex items-center justify-center text-2xl">
                + {{ attachments.length - 4 }} more
            </div>

            <a @click.stop :href="route('post.download', attachment)" class="z-20 opacity-0 group-hover:opacity-100 transitions-all w-8 h-8 flex items-center justify-center cursor-pointer absolute top-2 right-2 bg-gray-600 hover:bg-gray-800 rounded text-gray-100">
                <ArrowDownTrayIcon class="w-4 h-4" />
            </a>

            <img v-if="isImage(attachment)" :src="attachment.url" class="object-contain aspect-square"/>

            <div v-else-if="isVideo(attachment)" class="relative flex justify-center items-center">
                <PlayCircleIcon class="absolute z-20 w-16 h-16 cursor-pointer text-white" />
                <div class="absolute left-0 top-0 w-full h-full bg-black/50 z-10"></div>
                <video :src="attachment.url"></video>
            </div>

            <template v-else class="flex flex-col justify-center items-center">
                <PaperClipIcon class="w-10 h-10 mb-3"/>
                <small>{{ attachment.name }}</small>
            </template>
        </div>
    </template>
</template>

<script setup>
import { PaperClipIcon, ArrowDownTrayIcon, PlayIcon, PlayCircleIcon } from '@heroicons/vue/24/solid';
import { isImage } from '@/helpers.js';
import { isVideo } from '@/helpers.js';

defineProps({
    attachments: Array
})

defineEmits(['attachmentClick'])

</script>

<style lang="scss" scoped>

</style>
