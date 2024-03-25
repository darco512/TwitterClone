<template>
    <Disclosure v-slot="{ open }">

        <div class="ck-content-output" :class="contentClass" v-if="!open" v-html="content.substring(0, 200)" />

        <template v-if="content && content.length > 200">
            <DisclosurePanel class="">
                <div class="ck-content-output" :class="contentClass" v-html="content" />
            </DisclosurePanel>
            <div class="flex justify-end">
                <DisclosureButton class="text-blue-500 hover:underline"
                >
                    {{ open ? "Read Less" : "Read More"}}
                </DisclosureButton>
            </div>
        </template>
    </Disclosure>
</template>

<script setup>
import { Disclosure, DisclosurePanel, DisclosureButton } from '@headlessui/vue';

defineProps({
    content: String,
    contentClass: String
})
</script>

<style lang="scss" scoped>

</style>

