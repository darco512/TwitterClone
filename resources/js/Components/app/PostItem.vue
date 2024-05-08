<script setup>
import { Disclosure, DisclosureButton, DisclosurePanel } from '@headlessui/vue'
import PostUserHeader from './PostUserHeader.vue';
import { router, useForm, usePage } from '@inertiajs/vue3';
import { ChatBubbleLeftRightIcon, HandThumbUpIcon } from '@heroicons/vue/24/outline';
import axiosClient from '@/axiosClient.js';
import ReadMoreReadLess from '../app/ReadMoreReadLess.vue';
import EditDeleteDropdown from '../app/EditDeleteDropdown.vue';
import PostAttachments from './PostAttachments.vue';
import CommentList from './CommentList.vue';
import { computed } from 'vue';
import UrlPreview from './UrlPreview.vue';
import { MapPinIcon } from '@heroicons/vue/24/solid';

const props = defineProps({
        post: Object
    })

const authUser = usePage().props    .auth.user;
const group = usePage().props.group;


const emit = defineEmits(['editClick', 'attachmentClick'])

const postBody = computed(() => {
    let content = props.post.body.replace(
        /(?:(\s+)|<p>)((#\w+)(?![^<]*<\/a>))/g,
        (match, group1, group2) => {
            const encodedGroup = encodeURIComponent(group2);
            return `${group1 || ''}<a href="/search/${encodedGroup}" class="hastag">${group2}</a>`;
            })
        return content
    })

const isPinned = computed(() => {
    if(group?.id) {
        return group?.pinned_post_id === props.post.id
    }

    return authUser?.pinned_post_id === props.post.id
})

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

    function pinUnpinPost() {
        const form = useForm({
            forGroup: group?.id
        })
        let isPinned = false;
        if(group?.id) {
            isPinned = group?.pinned_post_id === props.post.id;
        } else {
            isPinned = authUser?.pinned_post_id === props.post.id;
        }

        form.post(route('post.pinUnpin', props.post.id), {
            preserveScroll: true,
            onSuccess: () => {
                if (group?.id) {
                    group.pinned_post_id = isPinned ? null : props.post.id
                }
                else {
                    authUser.pinned_post_id = isPinned ? null : props.post.id
                }
            }
        })
    }

</script>
<template>
    <div class="bg-white border rounded p-4 shadow mb-3">
        <div class="flex items-center justify-between mb-3">
            <PostUserHeader :post="post" />
            <div class="flex items-center">
                <template v-if="isPinned" class="flex items-center text-xs">
                    <MapPinIcon
                        class="h-3 w-3"
                        aria-hidden="true"
                    />
                    pinned
                </template>
                <EditDeleteDropdown :user="post.user" :post="post" @edit="openEditModal" @delete="deletePost" @pin="pinUnpinPost" />
            </div>
        </div>
        <div class="mb-3">
            <ReadMoreReadLess :content="postBody"/>
            <UrlPreview :preview="post.preview" :url="post.preview_url"/>
        </div>
        <div class="grid gap-3" :class="[
            post.attachments.length === 1 ? 'grid-cols-1' : 'grid-cols-2'
        ]">
            <PostAttachments :attachments="post.attachments" @attachmentClick="openAttachment"/>
        </div>

        <!-- Like and commnet buttons -->

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
        <DisclosurePanel class="comment-list mt-3 max-h-[400px] overflow-auto">
            <CommentList :post="post" :data="{comments: post.comments}"/>
        </DisclosurePanel>
      </Disclosure>
    </div>

</template>


<style lang="scss" scoped>

</style>
