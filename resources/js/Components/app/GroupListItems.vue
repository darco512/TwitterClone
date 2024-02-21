<script setup>
    import { ref } from 'vue';
    import GroupItem from '@/Components/app/GroupItem.vue';
    import TextInput from '@/Components/TextInput.vue';

    const searchKeyword = ref('')
</script>

<template>
    <TextInput :model-value="searchKeyword" placeholder="Type to search" class="w-full mt-4"/>
    <pre>{{ searchKeyword }}</pre>
    <div class="mt-3 h-[300px] lg:h-full overflow-auto">
        <div v-if="false" class="text-gray-400 text-center p-3">
        You are not joined to any groups
        </div>
        <div v-else>
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Laravel Developer" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Vue.js Developers" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Laravel Developer" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Vue.js Developers" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Laravel Developer" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Vue.js Developers" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Laravel Developer" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Vue.js Developers" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Laravel Developer" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />
            <GroupItem 
                image="https://picsum.photos/100" 
                title="Vue.js Developers" 
                description="Lorem ipsum dolor sit amet, consectetur adipisicing elit"
            />

        </div>
    </div>
</template>

<style lang="scss" scoped>

</style>