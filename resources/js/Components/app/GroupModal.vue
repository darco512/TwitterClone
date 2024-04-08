<template>

    <teleport to="body">
        <TransitionRoot appear :show="show" as="template">
        <Dialog as="div" @close="closeModal" class="relative z-50">
            <TransitionChild
            as="template"
            enter="duration-300 ease-out"
            enter-from="opacity-0"
            enter-to="opacity-100"
            leave="duration-200 ease-in"
            leave-from="opacity-100"
            leave-to="opacity-0"
            >
            <div class="fixed inset-0 bg-black/25" />
            </TransitionChild>

            <div class="fixed inset-0 overflow-y-auto">
            <div
                class="flex min-h-full items-center justify-center p-4 text-center"
            >
                <TransitionChild
                as="template"
                enter="duration-300 ease-out"
                enter-from="opacity-0 scale-95"
                enter-to="opacity-100 scale-100"
                leave="duration-200 ease-in"
                leave-from="opacity-100 scale-100"
                leave-to="opacity-0 scale-95"
                >
                <DialogPanel
                    class="w-full max-w-md transform overflow-hidden rounded bg-white text-left align-middle shadow-xl transition-all"
                >
                    <DialogTitle
                    as="h3"
                    class="flex items-center justify-between py-3 px-4 font-medium bg-gray-100 leading-6 text-gray-900"
                    >
                        Create New Group
                        <button
                            class="w-8 h-8 rounded-full hover:bg-black/5 transition flex items-center justify-center"
                            @click="closeModal"
                        >
                            <XMarkIcon class="w-4 h-4"/>
                        </button>
                    </DialogTitle>
                    <div class="p-4">
                        <div class="mb-3">
                            <label>Group Name</label>
                            <TextInput
                            type="text"
                            class="mt-1 block w-full"
                            v-model="form.name"
                            required
                            autofocus
                        />
                        </div>
                        <div class="mb-3">
                            <label class="flex items-center">
                            <Checkbox class="mr-3" name="remember" v-model:checked="form.auto_approval" />
                            Enable Auto Approval
                        </label>
                        </div>
                        <div class="mb-3">
                            <label>About Group</label>
                            <InputTextarea v-model="form.about" class="w-full"/>
                        </div>
                    </div>

                    <div class="flex justify-end gap-2 py-3 px-4">
                        <button
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
                </DialogPanel>
                </TransitionChild>
            </div>
            </div>
        </Dialog>
        </TransitionRoot>
    </teleport>
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
