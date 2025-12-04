<script setup>
import { defineProps, ref, computed, onMounted } from 'vue'

const props = defineProps({
    showPicker: Boolean
});

const emit = defineEmits(['close', 'selected'])

const closeModal = () => {
    emit('close');
    resetFilter()
}
const item = ref([
    {
        "id": 1,
        "name": "Vintage Camera",
        "color": "#E6E6FA",
        "category": "Photography",
        "tags": ["analog", "retro", "collectible"]
    },
    {
        "id": 2,
        "name": "Mountain Bike",
        "color": "#87CEFA",
        "category": "Sports",
        "tags": ["outdoor", "adventure", "cycling"]
    },
    {
        "id": 3,
        "name": "Leather Wallet",
        "color": "#FFDAB9",
        "category": "Accessories",
        "tags": ["handmade", "minimal", "fashion"]
    },
    {
        "id": 4,
        "name": "Leather Wallet",
        "color": "#F5FFFA",
        "category": "Accessories",
        "tags": ["handmade", "minimal", "fashion"]
    },
    {
        "id": 5,
        "name": "Wireless Headphones",
        "color": "#FAFAD2",
        "category": "Electronics",
        "tags": ["audio", "bluetooth", "portable"]
    },
    {
        "id": 6,
        "name": "Sketchbook",
        "color": "#F0FFF0",
        "category": "Art Supplies",
        "tags": ["drawing", "illustration", "paper"]
    },
    {
        "id": 7,
        "name": "Leather Wallet",
        "color": "#FFE4E1",
        "category": "Accessories",
        "tags": ["handmade", "minimal", "fashion"]
    },
    {
        "id": 8,
        "name": "Leather Jacket",
        "color": "#FFB6C1",
        "category": "Accessories",
        "tags": ["minimal", "fashion"]
    }
])


const allCategories = computed(() => ["All", ...new Set(item.value.map((i) => i['category']))]);

const allTags = computed(() => {
    const result = { all: [] };
    item.value.forEach((i) => {
        result.all.push(...i.tags);
        if (!result[i.category.toLowerCase()]) {
            result[i.category.toLowerCase()] = [];
        }
        result[i.category.toLowerCase()].push(...i.tags);
    })
    result.all = ['all', ...new Set(result.all)];

    for (let category in result) {
        if (category != "all") {
            result[category] = ["all", ...new Set(result[category])];
        }
    }
    return result;
})


const filterCategory = ref('all')
const filterTag = ref('all')
const filterSearch = ref('')
const currentSelection = ref(null)

const selectionHandler = (item) => {
    currentSelection.value = item;
    emit('selected', item);
}

const resetFilter = () => {
    filterCategory.value = 'all'
    filterTag.value = 'all'
    filterSearch.value = ''
}

const currentTags = computed(() => {
    return allTags.value[filterCategory.value]
})

const filterData = computed(() => {
    let result = item.value;
    if (filterCategory.value !== 'all') {
        result = result.filter((i) => i.category.toLowerCase() === filterCategory.value);
    }
    if (filterTag.value !== 'all') {
        result = result.filter((i) => i.tags.includes(filterTag.value));
    }
    if (filterSearch.value) {
        result = result.filter((i) => i.name.toLowerCase().includes(filterSearch.value.toLowerCase()));
    }
    return result;
})


</script>
<template>
    <!-- teleport component to the main body -->
    <Teleport to="body">
        <Transition enter-active-class="duration-300 transition ease-in-out" enter-from-class="opacity-0"
            enter-to-class="opacity-100" leave-active-class="duration-300 transition ease-in-out"
            leave-from-class="opacity-100" leave-to-class="opacity-0">
            <!-- check -->
            <div v-if="showPicker" class=" fixed inset-0 bg-gray-900/70 grid place-items-center" @click.self="closeModal">


                <div class="bg-white max-w-7xl rounded-lg w-full p-8 transition">
                    <div class="flex justify-between">
                    <h2 class="font-semibold text-2xl">Select Item</h2>

                    <button @click="closeModal" class="text-black hover:text-blue-600 cursor-pointer transition">
                        <svg xmlns="http://www.w3.org/2000/svg" width="28" height="28"  fill="none" viewBox="0 0 28 28">
                            <rect width="27.5" height="27.5"  class="fill-blue-100" rx="13.75"/>
                            <path class="stroke-current" stroke-linecap="round" stroke-linejoin="round" stroke-opacity=".8" stroke-width="1.2" d="M17.5 10 10 17.5m0-7.5 7.5 7.5"/>
                        </svg>
                    </button>
                    </div>
                    <div>
                        <div class="grid grid-cols-3">
                            <div class=" my-5 flex gap-3 col-span-2 flex-wrap">
                                <button @click="filterCategory = item.toLowerCase(); filterTag = 'all'"
                                    class="px-4 py-2 ring  rounded-lg h-fit capitalize transition cursor-pointer hover:bg-blue-500 hover:text-white"
                                    :class="filterCategory === item.toLowerCase() ? 'bg-blue-100 text-blue-600 ring-blue-600' : 'ring-gray-200'"
                                    v-for="item in allCategories" :key="item">{{ item }}</button>
                            </div>
                            <div class="my-5 text-right">
                                <input type="text" v-model="filterSearch" placeholder="Search"
                                    class="w-full px-4 py-2 ring ring-gray-200 rounded-lg transition">
                            </div>
                        </div>

                        <div class="flex gap-3 mb-4 pb-2 overflow-x-auto [&::-webkit-scrollbar]:h-1
        [&::-webkit-scrollbar-track]:bg-blue-100
        [&::-webkit-scrollbar-thumb]:bg-blue-300">
                            <button @click="filterTag = i" v-for="i in currentTags" :key="i"
                                :class="filterTag === i ? 'bg-blue-200 text-blue-600' : ''"
                                class="px-2 py-1 rounded-lg h-fit capitalize transition cursor-pointer hover:bg-blue-500 hover:text-white">
                                {{ i }}
                            </button>
                        </div>


                        
                        <transition-group
                        tag="ul"
                                class="flex gap-4 flex-wrap h-[50dvh] overflow-y-auto p-1"
                            enter-active-class="duration-300 ease-in-out"
                            leave-active-class="duration-300 ease-in-out"
                            enter-from-class="scale-70 opacity-0"
                            enter-to-class="scale-100 opacity-100"
                            leave-from-class="scale-100 opacity-100"
                            leave-to-class="scale-70 opacity-0"
                        >
                            <li @click="selectionHandler(item)" v-for="item in filterData" :key="item.id"
                                :style="{ backgroundColor: item.color }" 
                                class="w-30 text-sm aspect-square rounded-lg ring p-3 bg-gray-100 cursor-pointer relative h-fit" :class="currentSelection && item.id === currentSelection.id ? 'outline outline-offset-3 outline-blue-500 outline-2' : '' " >
                                {{ item.name }}

                                <div v-if="currentSelection && item.id === currentSelection.id"
                                    class="text-blue-600 absolute bottom-2 right-2 aspect-square w-5 grid place-items-center bg-blue-500 rounded-full">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="10" height="8" fill="none" viewBox="0 0 10 8">
                                        <path stroke="#fff" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.2" d="m.6 4.1 2.5 2.5 5.5-6"/>
                                    </svg>
                                </div>
                            </li>

                        </transition-group>
                    </div>

                </div>

            </div>
        </Transition>
    </Teleport>

</template>
