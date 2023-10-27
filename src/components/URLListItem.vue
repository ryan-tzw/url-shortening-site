<script setup lang="ts">
// Interfaces
interface URLObject {
    uuid: string
    original: string
    shortened: string
}
interface Props {
    index: number
    item: URLObject
}
// interface Emits {
//     (e: 'remove', id: number): void
// }
// const emit = defineEmits<Emits>()

const props = defineProps<Props>()

const index = props.index
const originalURL = props.item.original
const shortenedURL = props.item.shortened

function copyToClipboard() {
    navigator.clipboard.writeText(shortenedURL)
    alert('Copied: ' + shortenedURL)
}
</script>

<template>
    <li class="url-list-item">
        <div>
            <p>{{ index }}</p>
            <a :href="shortenedURL">{{ shortenedURL }}</a>
            <p>{{ originalURL }}</p>
            <button id="copy-button" @click="copyToClipboard">Copy</button>
            <button id="remove-button" @click="$emit('removeUrl', index)">
                Remove
            </button>
        </div>
    </li>
</template>

<style></style>
