<script setup lang="ts">
// Interfaces
interface URLObject {
    uuid: string
    original: string
    shortened: string
}
interface Props {
    item: URLObject
}

const props = defineProps<Props>()

const uuid = props.item.uuid
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
            <p>{{ shortenedURL }}</p>
            <!-- <a :href="shortenedURL">{{ shortenedURL }}</a> -->
            <p>{{ originalURL }}</p>
            <button id="copy-button" @click="copyToClipboard">Copy</button>
            <button id="remove-button" @click="$emit('removeUrl', uuid)">
                Remove
            </button>
        </div>
    </li>
</template>

<style>
.url-list-item {
    border: 1px solid red;
    margin-bottom: 20px;
    list-style: none;
}
</style>
