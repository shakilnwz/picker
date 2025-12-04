<script setup>
import Modal from './components/Modal.vue'
import { ref } from 'vue'
const showModal = ref(true)
const selected = ref(null)

const openModal = () => {
    showModal.value = true
}
const hideModal = () => {
    showModal.value = false
}

const selectedItem = (item) => {
    selected.value = item
}
</script>

<template>
    <div class="max-w-7xl mx-auto p-25 grid place-items-center">
        <button @click="openModal" class="px-6 py-3 rounded-lg bg-black text-white hover:bg-gray-300 hover:text-black ring cursor-pointer font-semibold">
            Open Picker
        </button>


        <div v-if="selected" class="text-center py-5 space-y-2">
            <p>Selected Item:</p>
            <span  class="block aspect-square p-4 rounded-lg ring" :style="{ backgroundColor: selected?.color }">{{ selected?.name }}</span>
        </div>
    </div>
    <Modal :showPicker="showModal" @close="hideModal" @selected="selectedItem" />

</template>

<style scoped></style>
