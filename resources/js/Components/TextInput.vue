<script setup>
import { onMounted, ref } from 'vue';


defineProps({
    modelValue: {
        type: String,
    },
    placeholder: String
})

defineEmits(['update:modelValue']);

const input = ref(null);

onMounted(() => {
    if (input.value.hasAttribute('autofocus')) {
        input.value.focus();
    }
});

defineExpose({ focus: () => input.value.focus() });
</script>

<template>
    <input
        class="dark:bg-gray-900 border-gray-300 dark:border-slate-800 dark:bg-slate-900 dark:text-slate-200 focus:border-indigo-500 focus:ring-indigo-500 rounded-md shadow-sm"
        :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
        ref="input"
        :placeholder="placeholder"
    />
</template>
