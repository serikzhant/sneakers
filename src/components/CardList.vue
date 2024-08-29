<script setup>
import Card from "./Card.vue";

defineProps({
    items: Array,
    isFavorites: Boolean,
});

const emit = defineEmits(["addToFavorites", "addToCart"]);
</script>

<template>
    <div class="grid grid-cols-4 gap-4" v-auto-animate>
        <Card
            v-for="item in items"
            :id="item.id"
            :key="item.id"
            :title="item.title"
            :price="item.price"
            :image-url="item.imageUrl"
            :on-click-favorite="
                isFavorites ? null : () => emit('addToFavorites', item)
            "
            :on-click-add="isFavorites ? null : () => emit('addToCart', item)"
            :is-added="item.isAdded"
            :is-favorite="item.isFavorite"
        />
    </div>
</template>
