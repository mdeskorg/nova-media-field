<template>
    <div
        class="flex flex-col items-center justify-center p-4 rounded-lg dark:bg-gray-900 bg-primary-50 relative"
        @dragover.prevent.stop="dragging = true"
        @dragleave.prevent.stop="dragging = false"
        @drop.prevent.stop="dropFiles"
        :class="{
            disabled: disabled,
        }"
    >
        <svg
            class="text-primary-500 pointer-events-none fill-current w-8 h-8 mb-4"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
        >
            <path
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M7.1 12.7l2.9-2.6 3 2.6-3-2.6V16"
            ></path>
            <path
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M15.1 16c2.2 0 3.9-1.7 3.9-3.9s-1.7-3.9-3.9-3.9c-.8 0-1.6.2-2.3.7-.6-3.2-3.7-5.4-6.9-4.8s-5.4 3.7-4.8 7c.6 2.8 3.1 4.9 6 4.9h8z"
            ></path>
        </svg>

        <p
            class="pointer-events-none text-center text-sm text-gray-500 dark:text-gray-400 font-semibold block mb-4"
        >
            {{
                multiple
                    ? __("Drag and drop a files here, or")
                    : __("Drag and drop a file here, or")
            }}:
        </p>

        <div
            class="flex justify-center items-center space-x-2"
            :class="{
                'pointer-events-none': dragging,
            }"
        >
            <!-- <div
                class="flex items-center justify-center p-2 cursor-pointer bg-white dark:bg-gray-800 border border-gray-500 dark:border-gray-800 rounded-lg text-gray-500 dark:text-gray-200 hover:opacity-75"
                @click.prevent.stop="emit('openFileManager')"
            >
                {{ __("File manager") }}
            </div> -->

            <label
                class="flex items-center justify-center p-2 cursor-pointer bg-white dark:bg-gray-800 border border-gray-500 dark:border-gray-800 rounded-lg text-gray-500 dark:text-gray-200 hover:opacity-75"
            >
                {{ multiple ? __("Choose Files") : __("Choose File") }}

                <input
                    :id="attribute"
                    class="visually-hidden"
                    @change.prevent="handleChange"
                    type="file"
                    ref="fileInput"
                    :multiple="multiple"
                    :accept="acceptedTypes"
                    :disabled="disabled"
                />
            </label>
        </div>

        <Transition>
            <div
                v-if="dragging"
                class="absolute pointer-events-none inset-0 z-50 flex items-center justify-center bg-white dark:bg-gray-800 rounded-lg w-full h-full border-2 border-primary-500"
            >
                <svg
                    class="text-primary-500 fill-current w-8 h-8 mr-4"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                >
                    <path
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M7.1 12.7l2.9-2.6 3 2.6-3-2.6V16"
                    ></path>
                    <path
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M15.1 16c2.2 0 3.9-1.7 3.9-3.9s-1.7-3.9-3.9-3.9c-.8 0-1.6.2-2.3.7-.6-3.2-3.7-5.4-6.9-4.8s-5.4 3.7-4.8 7c.6 2.8 3.1 4.9 6 4.9h8z"
                    ></path>
                </svg>

                <span
                    class="text-sm text-gray-500 dark:text-gray-400 font-semibold"
                >
                    {{ __("Drop your files here") }}
                </span>
            </div>
        </Transition>
    </div>
</template>

<script setup>
import { ref } from "vue";

const props = defineProps({
    helpText: {
        type: String,
    },
    attribute: {
        type: String,
        required: true,
    },
    acceptedTypes: {
        type: String,
        default: null,
    },
    multiple: {
        type: Boolean,
        default: false,
    },
    disabled: {
        type: Boolean,
        default: false,
    },
});

const emit = defineEmits(["openFileManager", "fileChanged"]);

const demFiles = ref([]);
const fileInput = ref();
const dragging = ref(false);

function dropFiles(e) {
    dragging.value = false;

    demFiles.value = props.multiple
        ? e.dataTransfer.files
        : [e.dataTransfer.files[0]];

    emit("fileChanged", demFiles.value);
}

const handleChange = () => {
    demFiles.value = props.multiple
        ? fileInput.value.files
        : [fileInput.value.files[0]];

    emit("fileChanged", demFiles.value);

    fileInput.value.files = null;
};
</script>

<style>
.pointer-events-none {
    pointer-events: none;
}
</style>
