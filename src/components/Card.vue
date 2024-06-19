<template>
    <div class="w-[200px] h-[300px] border border-[#2e2d2dad] rounded-lg flex justify-center items-center bg-[#FFFFFF]">
        <p class="text-5xl" :class="suitColor">
            <span v-html="suit"></span>
            {{ card.rank }}
            <span class="text-sm">{{ card }}</span>
        </p>
    </div>
</template>

<script setup>
import { computed } from 'vue'
const props = defineProps({
    card: {
        type: Object,
        required: true
    }
})

const suit = computed(() => {
    if (props.card.suit === "hearts") return '&hearts;'
    else if (props.card.suit === "clubs") return '&clubs;'
    else if (props.card.suit === "spades") return '&spades;'
    else return '&diams;'
})

const suitColor = computed(() => {
    if (props.card.suit === "hearts" || props.card.suit === "diamonds") return 'text-[#ff5723]'
    else return 'text-[#000000]'
})

</script>

<style lang="scss" scoped></style>