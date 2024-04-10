<template>
    <AuthenticatedLayout>
        <div class="p-16 text-center">
            <h1 class="text-2xl">{{ title }}</h1>
            <div>{{ body }}</div>
        </div>
    </AuthenticatedLayout>
</template>

<script setup>
import AuthenticatedLayout from '../Layouts/AuthenticatedLayout.vue';

defineProps({
    title: String,
    description: String
})
</script>

<style lang="scss" scoped>

</style>
