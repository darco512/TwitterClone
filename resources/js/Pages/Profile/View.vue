<template>
    <AuthenticatedLayout>
        <div class="max-w-[768px] mx-auto h-full overflow-auto">
            <div class="px-4">
                <div
                    v-show="showNotification && success"
                    class="my-2 py-2 px-3 font-medium text-sm bg-emerald-500 text-white"
                >
                    {{ success }}
                </div>
                <div
                    v-if="errors.cover"
                    class="my-2 py-2 px-3 font-medium text-sm bg-red-500 text-white"
                >
                    {{ errors.cover }}
                </div>
                <div class="relative group bg-white dark:bg-slate-950 dark:text-gray-100">
                    <img :src="coverImageSrc || user.cover_url || '/image/default_cover.jpg'" class="w-full h-[200px] object-cover"/>
                    <div class="absolute top-2 right-2">
                        <button v-if="!coverImageSrc" class="bg-gray-50 hover:bg-gray-100 text-gray-800 py-1 px-2 text-xs flex items-center opacity-0 group-hover:opacity-100">
                            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-3 h-3 mr-2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6.827 6.175A2.31 2.31 0 0 1 5.186 7.23c-.38.054-.757.112-1.134.175C2.999 7.58 2.25 8.507 2.25 9.574V18a2.25 2.25 0 0 0 2.25 2.25h15A2.25 2.25 0 0 0 21.75 18V9.574c0-1.067-.75-1.994-1.802-2.169a47.865 47.865 0 0 0-1.134-.175 2.31 2.31 0 0 1-1.64-1.055l-.822-1.316a2.192 2.192 0 0 0-1.736-1.039 48.774 48.774 0 0 0-5.232 0 2.192 2.192 0 0 0-1.736 1.039l-.821 1.316Z" />
                                <path stroke-linecap="round" stroke-linejoin="round" d="M16.5 12.75a4.5 4.5 0 1 1-9 0 4.5 4.5 0 0 1 9 0ZM18.75 10.5h.008v.008h-.008V10.5Z" />
                            </svg>

                            Update Cover Imange

                            <input type="file" class="absolute left-0 top-0 right-0 bottom-0 opacity-0 cursor-pointer z-2" @change="onCoverChange" />
                        </button>
                        <div v-else class="flex gap-2 opacity-0 group-hover:opacity-100">
                            <button
                                class="bg-gray-50 hover:bg-gray-100 text-gray-800 py-1 px-2 text-xs flex items-center"
                                @click="resetCoverImage"
                            >
                                <XMarkIcon class="h-3 w-3 mr-2" />
                                Cancel
                            </button>
                            <button
                                class="bg-gray-800 hover:bg-gray-900 text-gray-100 py-1 px-2 text-xs flex items-center"
                                @click="submitCoverImage"
                            >
                                <CheckCircleIcon class="h-3 w-3 mr-2" />
                                Submit
                            </button>
                        </div>
                    </div>
                    <div>
                        <div class="flex">
                            <div class="flex items-center justify-center relative group/avatar ml-[48px] w-[128px] h-[128px] -mt-[64px] rounded-full">
                                <img
                                    :src="avatarImageSrc || user.avatar_url || '/image/default_avatar.webp'"
                                    class="w-full h-full object-cover rounded-full"
                                />
                                <button v-if="!avatarImageSrc" class="absolute flex items-center justify-center left-0 right-0 top-0 bottom-0 bg-black/50 text-gray-200 rounded-full opacity-0 group-hover/avatar:opacity-100">
                                    <CameraIcon class="w-8 h-8"/>
                                    <input
                                        type="file"
                                        class="absolute left-0 top-0 right-0 bottom-0 opacity-0 cursor-pointer z-2"
                                        @change="onAvatarChange"
                                    />
                                </button>
                                <div v-else class="absolute top-1 right-0 flex flex-col gap-2">
                                    <button
                                        class="w-7 h-7 flex items-center justify-center bg-red-500/80 text-white rounded-full"
                                        @click="resetAvatarImage"
                                    >
                                        <XMarkIcon class="h-5 w-5" />
                                    </button>
                                    <button
                                        class="w-7 h-7 flex items-center justify-center bg-emerald-500/80 text-white rounded-full"
                                        @click="submitAvatarImage"
                                    >
                                        <CheckCircleIcon class="h-5 w-5" />
                                    </button>
                                </div>
                            </div>
                            <div class="flex justify-between items-center flex-1 p-4">
                                <div>
                                    <h2 class="font-bold text-lg">{{ user.name }}</h2>
                                    <p class="text-xs text-gray-500">{{ props.followerCount }} follower(s)</p>
                                </div>
                                <div v-if="!isMyProfile">
                                    <DangerButton v-if="isCurrentUserFollower" @click="followUser">
                                        Unfollow
                                    </DangerButton>
                                    <PrimaryButton v-else @click="followUser">
                                        Follow
                                    </PrimaryButton>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="border-t m-4 mt-0">
                <TabGroup>
                    <TabList class="flex bg-white dark:bg-slate-950 dark:text-white">
                        <Tab v-slot="{ selected }" as="template">
                            <TabItem text="Posts" :selected="selected" />
                        </Tab>
                        <Tab v-slot="{ selected }" as="template">
                            <TabItem text="Followers" :selected="selected" />
                        </Tab>
                        <Tab v-slot="{ selected }" as="template">
                            <TabItem text="Following" :selected="selected" />
                        </Tab>
                        <Tab v-slot="{ selected }" as="template">
                            <TabItem text="Photos" :selected="selected" />
                        </Tab>
                        <Tab v-if="isMyProfile" v-slot="{ selected }" as="template">
                            <TabItem text="My Profile" :selected="selected" />
                        </Tab>
                    </TabList>

                    <TabPanels class="mt-2">
                        <TabPanel>
                            <template v-if="posts">
                                <CreatePost />
                                <PostList :posts="posts.data" class="flex-1" />
                            </template>
                            <div v-else class="py-8 text-center">
                                You don't have permission to view these posts.
                            </div>
                        </TabPanel>
                        <TabPanel>
                            <div class="mb-3">
                                <TextInput :model-value="searchFollowers" placeholder="Type to search" class="w-full"/>
                            </div>
                            <div v-if="followers.length"v class="grid grid-cols-2 gap-3">
                                <UserItem
                                    v-for="user of followers"
                                    :user="user"
                                    :key="user.id"
                                    class="shadow rounded-lg"
                                />
                            </div>
                            <div v-else class="text-center py-8">
                                The user doesn't have any followers
                            </div>
                        </TabPanel>
                        <TabPanel>
                            <div class="mb-3">
                                <TextInput :model-value="searchFollowings" placeholder="Type to search" class="w-full"/>
                            </div>
                            <div v-if="followings.length" class="grid grid-cols-2 gap-3">
                                <UserItem
                                    v-for="user of followings"
                                    :user="user"
                                    :key="user.id "
                                    class="shadow rounded-lg"
                                />
                            </div>
                            <div v-else class="text-center py-8">
                                The user is not following to anybody
                            </div>
                        </TabPanel>
                        <TabPanel>
                            <TabPhotos :photos="photos"/>
                        </TabPanel>
                        <TabPanel v-if="isMyProfile">
                            <Edit :must-verify-email="mustVerifyEmail" :status="status"/>
                        </TabPanel>
                    </TabPanels>
                </TabGroup>
            </div>
        </div>
    </AuthenticatedLayout>

  </template>

<script setup>
import { computed, ref} from 'vue'
import { TabGroup, TabList, Tab, TabPanels, TabPanel } from '@headlessui/vue'
import { useForm, usePage } from '@inertiajs/vue3';
import AuthenticatedLayout from '../../Layouts/AuthenticatedLayout.vue';
import TabItem from './Partials/TabItem.vue';
import Edit from './Edit.vue';
import { XMarkIcon, CheckCircleIcon, CameraIcon } from '@heroicons/vue/24/solid'
import PrimaryButton from '../../Components/PrimaryButton.vue';
import DangerButton from '../../Components/DangerButton.vue';
import CreatePost from '../../Components/app/CreatePost.vue';
import PostList from '../../Components/app/PostList.vue';
import UserItem from '../../Components/app/UserItem.vue';
import TextInput from '../../Components/TextInput.vue';
import PostAttachments from '../../Components/app/PostAttachments.vue';
import TabPhotos from './TabPhotos.vue';

const imagesForm = useForm({
    avatar: null,
    cover: null
})

const showNotification = ref(true)
const coverImageSrc = ref('')
const avatarImageSrc = ref('')
const searchFollowers = ref('')
const searchFollowings = ref('')
const authUser = usePage().props.auth.user;

const isMyProfile = computed(() => authUser && authUser.id === props.user.id)

const props = defineProps({
    errors: Object,
    mustVerifyEmail: {
        type: Boolean,
    },
    status: {
        type: String,
    },
    success: {
        type: String,
    },
    isCurrentUserFollower: Boolean,
    followerCount: Number,
    user: {
        type: Object
    },
    posts: Object,
    followers: Array,
    followings: Array,
    photos: Array
});

function onCoverChange(event) {
    imagesForm.cover = event.target.files[0];
    if(imagesForm.cover) {
        const reader = new FileReader()
        reader.onload = () => {
            coverImageSrc.value = reader.result;
        }
        reader.readAsDataURL(imagesForm.cover)
    }
}

function onAvatarChange(event) {
    imagesForm.avatar = event.target.files[0];
    if(imagesForm.avatar) {
        const reader = new FileReader()
        reader.onload = () => {
            avatarImageSrc.value = reader.result;
        }
        reader.readAsDataURL(imagesForm.avatar)
    }
}

function resetCoverImage() {
    imagesForm.cover=null;
    coverImageSrc.value=null;
}resetAvatarImage

function resetAvatarImage() {
    imagesForm.avatar=null;
    avatarImageSrc.value=null;
}

function submitCoverImage() {
    imagesForm.post(route('profile.updateImages'), {
        preserveScroll: true,
        onSuccess: (user) => {
            resetCoverImage()
            showNotification.value = true
            setTimeout(() =>{
            showNotification.value = false;
        }, 5000)
        }
    })
}

function submitAvatarImage() {
    imagesForm.post(route('profile.updateImages'), {
        preserveScroll: true,
        onSuccess: (user) => {
            resetAvatarImage()
            showNotification.value = true
            setTimeout(() =>{
            showNotification.value = false;
        }, 5000)
        }
    })
}

function followUser() {
    const form = useForm({
        follow: !props.isCurrentUserFollower
    })

    form.post(route('user.follow', props.user.id), {
        preserveScroll: true
    })
}


</script>

<style lang="scss" scoped>

</style>
