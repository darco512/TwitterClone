<script setup>
import { onMounted, ref } from 'vue';

const model = defineModel({
    type: String,
    required: false,
    placeholder: String
});


const input = ref(null);

onMounted(() => {
    onInputChange()
    if (input.value.hasAttribute('autofocus')) {
        input.value.focus();
    }
    
});

defineExpose({ focus: () => input.value.focus() });


function onInputChange($event) {

    input.value.style.height = 'auto';
    input.value.style.height = input.value.scrollHeight + 'px';
}


</script>

<template>
    <textarea
        class="border-gray-300 focus:border-indigo-500 focus:ring-indigo-500 rounded-md shadow-sm"
        v-model="model"
        @input="onInputChange"
        ref="input"
        placeholder="placeholder"
        style="resize: none; overflow-y: hidden;"
    ></textarea>
</template>
