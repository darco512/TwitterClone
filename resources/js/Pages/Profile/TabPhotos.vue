<template>
    <div class="grid gap-2 grid-cols-2 sm:grid-cols-3">
        <template v-for="(attachment, ind) of photos" >
            <div
                @click="openPhoto(ind)"
                class="group bg-blue-100 aspect-square flex flex-col items-center justify-center text-gray-500 relative mb-3"
            >


                <a @click.stop :href="route('post.download', attachment)" class="z-20 opacity-0 group-hover:opacity-100 transitions-all w-8 h-8 flex items-center justify-center cursor-pointer absolute top-2 right-2 bg-gray-600 hover:bg-gray-800 rounded text-gray-100">
                    <ArrowDownTrayIcon class="w-4 h-4" />
                </a>

                <img v-if="isImage(attachment)" :src="attachment.url" class="object-contain aspect-square"/>

                <template v-else class="flex flex-col justify-center items-center">
                    <PaperClipIcon class="w-10 h-10 mb-3"/>
                    <small>{{ attachment.name }}</small>
                </template>
            </div>
            <div v-if="!photos.length" class="py-8 text-center text-gray-600">
                There are no photos
            </div>
        </template>
    </div>
    <AttachmentPreviewModal
        :attachments="photos || []"
        v-model:index="currentPhotoIndex"
        v-model="showModal"
    />
</template>

<script setup>
import { PaperClipIcon, ArrowDownTrayIcon } from '@heroicons/vue/24/solid';
import { isImage } from '@/helpers.js';
import { ref } from 'vue';
import AttachmentPreviewModal from '../../Components/app/AttachmentPreviewModal.vue';

defineProps({
    photos: Array
})

const currentPhotoIndex  = ref(0)
const showModal = ref(false)

function openPhoto(index) {
    currentPhotoIndex.value = index
    showModal.value = true
}
</script>

<style lang="scss" scoped>

</style>
