<script setup>
import { Disclosure, DisclosureButton, DisclosurePanel } from '@headlessui/vue'
import FollowingListItems from '@/Components/app/FollowingListItems.vue';

defineProps({
    users: Array
    })
</script>

<template>

    <div class="px-3 bg-white dark:bg-slate-950 rounded border dark:border-slate-900 py-3 dark:text-gray-100 h-full overflow-hidden">
        <div class="block lg:hidden flex flex-col">
            <Disclosure v-slot="{ open }">
                <DisclosureButton class="flex justify-between items-center">
                    <h2 class="text-xl font-bold">My Followings</h2>
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6 transition-all" :class="open ? 'rotate-180 transform' : '' " >
                        <path fill-rule="evenodd" d="M12.53 16.28a.75.75 0 0 1-1.06 0l-7.5-7.5a.75.75 0 0 1 1.06-1.06L12 14.69l6.97-6.97a.75.75 0 1 1 1.06 1.06l-7.5 7.5Z" clip-rule="evenodd" />
                    </svg>


                </DisclosureButton>
                <DisclosurePanel >
                    <FollowingListItems :users="users"/>
                </DisclosurePanel>
            </Disclosure>
        </div>
        <div class="hidden lg:block h-full overflow-hidden lg:flex flex-col">
            <h2 class="text-xl font-bold">My Followings</h2>
            <FollowingListItems :users="users"/>
        </div>
    </div>
</template>

<style>

</style>
