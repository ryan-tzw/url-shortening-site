<script setup lang="ts">
import { ref, onMounted } from 'vue'
import URLListItem from './URLListItem.vue'

interface URLObject {
    uuid: string
    name: string
    original: string
    shortened: string
}

const localStorageKey = 'urlStorage'
const inputURL = ref('')
let urlArray = ref<URLObject[]>([])

function validateURL(inputURL: string) {
    const regexp =
        /[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)?/gi

    if (!inputURL.match(regexp)) {
        alert('Please enter a valid URL. ')
        console.log(inputURL)
    } else {
        shortenURL(inputURL)
    }
}

function shortenURL(inputURL: string) {
    var endpointURL = new URL(
        'https://csclub.uwaterloo.ca/~phthakka/1pt-express/addURL'
    )
    // 'long' is just the name of the key that the API accepts
    endpointURL.searchParams.set('long', inputURL)

    fetch(endpointURL.href, { method: 'POST' })
        .then((response) => response.json())
        .then((json) => {
            // After getting JSON response
            const baseURL = 'https://1pt.co/'
            const shortenedURL = new URL(json.short, baseURL)

            addURL(inputURL, shortenedURL.href)
        })
}

function addURL(originalURL: string, shortenedURL: string) {
    const uuid = crypto.randomUUID()

    // Create new object
    const newItem: URLObject = {
        uuid: uuid,
        name: originalURL,
        original: originalURL,
        shortened: shortenedURL,
    }

    // Add new addition to the start of the urlArray
    urlArray.value.unshift(newItem)

    updateLocalStorage()
}

function removeURL(uuid: string) {
    function findByUuid(item: URLObject) {
        return item.uuid === uuid
    }
    const index = urlArray.value.findIndex(findByUuid)

    console.log(index)
    urlArray.value.splice(index, 1)
    updateLocalStorage()
}

function updateLocalStorage() {
    // Update the localStorage with the new urlArray
    const urlArrayJSON = JSON.stringify(urlArray.value)
    localStorage.setItem(localStorageKey, urlArrayJSON)
}

onMounted(() => {
    const localStorageItem = localStorage.getItem(localStorageKey)
    if (localStorageItem != null) {
        const parsed = JSON.parse(localStorageItem)
        urlArray.value = parsed
    } else {
        console.log('Nothing in storage')
    }
})
</script>

<template>
    <div class="input-container">
        <input
            v-model="inputURL"
            id="url-input"
            type="text"
            placeholder="Enter a URL"
            v-on:keyup.enter="validateURL(inputURL)"
        />
        <button id="submit-url" v-on:click="validateURL(inputURL)">
            Submit
        </button>
    </div>
    <div class="list-container">
        <ul id="url-list">
            <URLListItem
                v-for="item in urlArray"
                :key="item.uuid"
                :item="item"
                @remove-url="removeURL"
            />
        </ul>
    </div>
</template>

<style scoped lang="scss">
// input {
// }
</style>
