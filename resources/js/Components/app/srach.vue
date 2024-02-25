<template v-for="(myFile, ind) of attachmentFiles.slice(0, 4)">
    <div class="group bg-blue-100 aspect-square flex flex-col items-center justify-center text-gray-500 relative">

        <div v-if="ind===3 && attachmentFiles.length > 4" class="absolute left-0 top-0 right-0 bottom-0 z-10 bg-black/60 text-white flex items-center justify-center text-2xl">
            + {{ attachmentFiles.length - 4 }} more
        </div>

        <button
            @click="removeFile(myFile)"
            class="absolute right-3 top-3 w-7 h-7 flex items-center justify-center bg-black/30 text-white rounded-full hover: bg-black/40 z-20"
        >
            <XMarkIcon class="w-5 h-5"/>
        </button>

        <img v-if="isImage(myFile.file)" :src="myFile.url" class="object-cover aspect-square"/>

        <template v-else>
            <PaperClipIcon class="w-10 h-10 mb-3" />
            <small class="text-center">{{ myFile.file.name }}</small>
        </template>
    </div>
</template>