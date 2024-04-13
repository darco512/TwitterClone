<script setup>
import { ref } from 'vue';
import TextInput from '@/Components/TextInput.vue';
import UserItem from './UserItem.vue';

    const searchKeyword = ref('')
</script>

<template>
    <TextInput :model-value="searchKeyword" placeholder="Type to search" class="w-full mt-4"/>
    <div class="mt-3 h-[300px] lg:h-full overflow-auto">
        <div v-if="false" class="text-gray-400 text-center p-3">
        You don't have friends yet.
        </div>
        <div v-else>
        </div>
    </div>
</template>

<style>

</style>
