<script setup>
import { Disclosure, DisclosureButton, DisclosurePanel } from '@headlessui/vue'
import PostUserHeader from './PostUserHeader.vue';
import { router, usePage } from '@inertiajs/vue3';
import { isImage } from '@/helpers.js'
import { ChatBubbleLeftEllipsisIcon, ChatBubbleLeftRightIcon, HandThumbUpIcon } from '@heroicons/vue/24/outline';
import { ArrowDownTrayIcon, PaperClipIcon } from '@heroicons/vue/24/solid';
import axiosClient from '@/axiosClient.js';
import InputTextarea from '../InputTextarea.vue';
import IndigoButton from '../app/IndigoButton.vue';
import ReadMoreReadLess from '../app/ReadMoreReadLess.vue';
import EditDeleteDropdown from '../app/EditDeleteDropdown.vue';
import { ref } from 'vue';

const authUser = usePage().props.auth.user;
const editingComment = ref(null);
const props = defineProps({
        post: Object
    })

const newCommentText = ref('')

const emit = defineEmits(['editClick', 'attachmentClick'])


    function openEditModal(){
        emit('editClick', props.post)
    }

    function deletePost(){
        if(window.confirm('Are you sure you want to delete this post?')) {
            router.delete(route('post.destroy', props.post), {
                preserveScroll: true
            })
        }
    }

    function openAttachment(index){
        emit('attachmentClick', props.post, index)
    }

    function sendReaction(){
        axiosClient.post(route('post.reaction', props.post), {
            reaction: 'like'
        })
        .then (({data})=>{
            props.post.current_user_has_reaction = data.current_user_has_reaction
            props.post.num_of_reactions = data.num_of_reactions
        })
    }

    function createComment() {
        axiosClient.post(route('post.comment.create', props.post), {
            comment: newCommentText.value
        })
        .then (({data})=>{
            newCommentText.value = '';
            props.post.comments.unshift(data)
            props.post.num_of_comments++
        })
    }

    function deleteComment(comment) {
        if (!window.confirm('Are you sure you want to delete this comment?')){
            return false;
        }
        axiosClient.delete(route('comment.delete', comment.id))
            .then (({data})=>{
                props.post.comments = props.post.comments.filter(c => c.id !== comment.id)
                props.post.num_of_comments--
            })
    }

    function startCommentEdit(comment) {
        console.log(comment);
        editingComment.value = {
            id: comment.id,
            comment: comment.comment.replace(/<br\s*\/?>/gi, '\n') // <br />, <br >, <br>, <br/>, <br      />
        }
    }

    function updateComment(comment) {
        axiosClient.put(route('comment.update', editingComment.value.id), editingComment.value)
            .then (({data})=>{

                editingComment.value = null

                props.post.comments = props.post.comments.map((c) => {
                    if(c.id === data.id) {
                        return data
                    }
                    return c;
                })
            })
    }

    function sendCommentReaction(comment) {
        axiosClient.post(route('comment.reaction', comment.id), {
            reaction: 'like'
        })
        .then (({data})=>{
            comment.current_user_has_reaction = data.current_user_has_reaction
            comment.num_of_reactions = data.num_of_reactions
        })
    }

</script>
<template>
    <div class="bg-white border rounded p-4 shadow mb-3">
        <div class="flex items-center justify-between mb-3">
            <PostUserHeader :post="post" />
            <EditDeleteDropdown :user="post.user" @edit="openEditModal" @delete="deletePost"/>
        </div>
        <div class="mb-3">
            <ReadMoreReadLess :content="post.body"/>
        </div>
        <div class="grid gap-3" :class="[
            post.attachments.length === 1 ? 'grid-cols-1' : 'grid-cols-2'
        ]">
            <template v-for="(attachment, ind) of post.attachments.slice(0, 4)" >
                <div
                    @click="openAttachment(ind)"
                    class="group bg-blue-100 aspect-square flex flex-col items-center justify-center text-gray-500 relative mb-3"
                >

                    <div v-if="ind===3 && post.attachments.length > 4" class="absolute left-0 top-0 right-0 bottom-0 z-10 bg-black/60 text-white flex items-center justify-center text-2xl">
                        + {{ post.attachments.length - 4 }} more
                    </div>

                    <a @click.stop :href="route('post.download', attachment)" class="z-20 opacity-0 group-hover:opacity-100 transitions-all w-8 h-8 flex items-center justify-center cursor-pointer absolute top-2 right-2 bg-gray-600 hover:bg-gray-800 rounded text-gray-100">
                        <ArrowDownTrayIcon class="w-4 h-4" />
                    </a>

                    <img v-if="isImage(attachment)" :src="attachment.url" class="object-contain aspect-square"/>

                    <template v-else class="flex flex-col justify-center items-center">
                        <PaperClipIcon class="w-10 h-10 mb-3"/>
                        <small>{{ attachment.name }}</small>
                    </template>
                </div>
            </template>
        </div>

        <Disclosure v-slot="{ open }">
            <div class="flex gap-2">
                <button
                    @click="sendReaction"
                    class="flex flex-1 gap-1 items-center py-2 px-4 justify-center text-gray-800 rounded-lg"
                    :class="[
                        post.current_user_has_reaction ? 'bg-sky-100 hover:bg-sky-200' : 'bg-gray-100 hover:bg-gray-200'
                    ]"
                >
                    <HandThumbUpIcon class="mr-2 h-5 w-5"/>
                    <span class="mr-2">{{ post.num_of_reactions }}</span>
                    {{ post.current_user_has_reaction ? 'Unlike' : 'Like' }}
                </button>
                <DisclosureButton
                    class="flex flex-1 gap-1 items-center py-2 px-4 justify-center text-gray-800 bg-gray-100 hover:bg-gray-200 rounded-lg"
                >
                    <ChatBubbleLeftRightIcon class="mr-2 h-5 w-5"/>
                    <span class="mr-2">{{ post.num_of_comments }}</span>
                    Comment
                </DisclosureButton>
            </div>
        <DisclosurePanel class="mt-3">
                <div>
                    <div class="flex gap-2 mb-3">
                        <a href="javascript:void(0)">
                            <img :src="authUser.avatar_url" class="w-[40px] rounded-full border border-2 transition-all hover:border-blue-500" />
                        </a>
                        <div class="flex flex-1">
                            <InputTextarea v-model="newCommentText" placeholder="Enter your comment here" rows="1" class="w-full max-h-[160px] resize-none rounded-r-none"/>
                            <IndigoButton @click="createComment" class="w-[100px] rounded-l-none">Submit</IndigoButton>
                        </div>
                    </div>
                </div>
                <div>
                    <div v-for="comment of post.comments" :key="comment.id" class="mb-4">
                        <div class="flex justify-between gap-2">
                            <div class="flex gap-2">
                                <a href="javascript:void(0)">
                                <img :src="comment.user.avatar_url" class="w-[40px] rounded-full border border-2 transition-all hover:border-blue-500" />
                            </a>
                            <div>
                                <h4 class="font-bold">
                                    <a href="javascript:void(0)" class="hover:underline">{{ comment.user.name }}</a>
                                </h4>
                            <small class="text-xs text-gray-400">{{ comment.updated_at }}</small>
                            </div>
                            </div>
                            <EditDeleteDropdown :user="comment.user" @edit="startCommentEdit(comment)" @delete="deleteComment(comment)"/>
                        </div>
                        <div class="pl-12">
                            <div v-if="editingComment && editingComment.id === comment.id" >
                                <InputTextarea  v-model="editingComment.comment" placeholder="Enter your comment here" rows="1" class="w-full max-h-[160px] resize-none"/>
                                <div class="flex gap-2 justify-end">
                                    <button @click="editingComment = null" class="w-[100px] text-indigo-500" >Cancel</button>
                                    <IndigoButton @click="updateComment" class="w-[100px]">Submit</IndigoButton>
                                </div>
                            </div>
                            <ReadMoreReadLess v-else :content="comment.comment" content-class="text-sm flex flex-1"/>
                            <div class="mt-1 flex gap-2">
                                <button
                                    @click="sendCommentReaction(comment)"
                                    class="flex items-center text-sm text-indigo-500 py-0.5 px-1 rounded-lg"
                                    :class="[
                                        comment.current_user_has_reaction ?
                                        'bg-indigo-50 hover:bg-indigo-100' :
                                        'hover:bg-indigo-50'
                                        ]"
                                >
                                    <HandThumbUpIcon class="w-4 h-4 mr-2"/>
                                    <span class="mr-2">{{ comment.num_of_reactions }}</span>
                                    {{ comment.current_user_has_reaction ? 'unlike' : 'like' }}
                                </button>
                                <button class="flex items-center text-sm text-indigo-500 py-0.5 px-1 hover:bg-indigo-100 rounded-lg">
                                    <ChatBubbleLeftEllipsisIcon class="w-4 h-4 mr-2"/>
                                    reply
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
        </DisclosurePanel>
      </Disclosure>
    </div>

</template>


<style lang="scss" scoped>

</style>
