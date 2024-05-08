<template>
    <BaseModal title="Create New Group" v-model="show" @hide="closeModal()">
        <div class="p-4 dark:text-gray-100">
            <GroupForm :form="form"/>
        </div>

        <div class="flex justify-end gap-2 py-3 px-4">
            <button @click="show=false"
                class="flex gap-1 items-center py-2 px-4 justify-center text-gray-800 bg-gray-100 hover:bg-gray-200 rounded-md"
            >
                <XMarkIcon class="mr-2 h-5 w-5"/>
                Cancel
            </button>
            <button
                type="button"
                class="flex items-center justify-center rounded-md bg-indigo-600 px-4 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
                @click="submit"
            >
                <BookmarkIcon class="w-4 h-4" />
                Submit
            </button>
        </div>
    </BaseModal>
  </template>

  <script setup>
import { computed, ref } from 'vue'
import {
TransitionRoot,
TransitionChild,
Dialog,
DialogPanel,
DialogTitle,
} from '@headlessui/vue'
import { BookmarkIcon, XMarkIcon } from '@heroicons/vue/24/solid';
import { useForm } from '@inertiajs/vue3';
import Checkbox from '@/Components/Checkbox.vue';
import TextInput from '@/Components/TextInput.vue';
import InputTextarea from '../InputTextarea.vue';
import axiosClient from '@/axiosClient.js';
import GroupForm from './GroupForm.vue';
import BaseModal from './BaseModal.vue';
  const props = defineProps({
    modelValue: Boolean
  })


  const formErrors = ref({})

  const form = useForm({
    name: '',
    auto_approval: true,
    about: '',
})

  const show = computed({
    get: () => props.modelValue,
    set: (value) => emit('update:modelValue', value)
})


  const emit = defineEmits(['update:modelValue', 'hide', 'create'])

  function closeModal() {
    show.value = false
    emit('hide')
    resetModal()
  }

  function resetModal() {
    form.reset()
    formErrors.value = {}
  }

  function submit() {
    axiosClient.post(route('group.create'), form)
        .then(({data}) => {
            closeModal();
            emit('create' , data)
        })
  }

  </script>
