<script setup>
import axios from "axios";
import { inject, reactive, watch, ref, onMounted } from "vue";

import CardList from "../components/CardList.vue";

const items = ref([]); // { value: [...]}

const { cart, addToCart, removeFromCart } = inject("cart");

const filters = reactive({
    sortBy: "title",
    searchQuery: "",
});

const onClickAddPlus = (item) => {
    if (!item.isAdded) {
        addToCart(item);
    } else {
        removeFromCart(item);
    }
};

const onSelectChange = (event) => {
    filters.sortBy = event.target.value;
};

const onChangeSearchInput = (event) => {
    filters.searchQuery = event.target.value;
};

const addToFavorites = async (item) => {
    try {
        if (!item.isFavorite) {
            const obj = {
                item_id: item.id,
            };
            item.isFavorite = true;
            const { data } = await axios.post(
                `https://d643f05a41667dbe.mokky.dev/favorites`,
                obj
            );

            item.favoriteId = data.id;
        } else {
            item.isFavorite = false;
            await axios.delete(
                `https://d643f05a41667dbe.mokky.dev/favorites/${item.favoriteId}`
            );
            item.favoriteId = null;
        }
    } catch (err) {
        console.log(err);
    }
};

const fetchItems = async () => {
    try {
        const params = {
            sortBy: filters.sortBy,
        };

        if (filters.searchQuery) {
            params.title = `*${filters.searchQuery}*`;
        }

        const { data } = await axios.get(
            `https://d643f05a41667dbe.mokky.dev/items`,
            { params }
        );

        items.value = data.map((obj) => ({
            ...obj,
            isFavorite: false,
            isAdded: false,
            favoriteId: null,
        }));
    } catch (err) {
        console.log(err);
    }
};

const fetchFavorites = async () => {
    try {
        const { data: favorites } = await axios.get(
            `https://d643f05a41667dbe.mokky.dev/favorites`
        );
        items.value = items.value.map((item) => {
            const favorite = favorites.find(
                (favorite) => favorite.item_id === item.id
            );

            if (!favorite) {
                return item;
            }

            return {
                ...item,
                isFavorite: true,
                favoriteId: favorite.id,
            };
        });
    } catch (err) {
        console.log(err);
    }
};

onMounted(async () => {
    const localCart = localStorage.getItem("cart");

    cart.value = localCart ? JSON.parse(localCart) : [];

    await fetchItems();
    await fetchFavorites();

    items.value = items.value.map((item) => ({
        ...item,

        isAdded: cart.value.some((cartItem) => cartItem.id === item.id),
    }));
});

watch(filters, fetchItems);

watch(cart, () => {
    items.value = items.value.map((item) => ({
        ...item,
        isAdded: false,
    }));
});
</script>

<template>
    <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

        <div class="flex gap-4">
            <select
                @change="onSelectChange"
                name=""
                id=""
                class="py-2 px-3 border rounded-md outline-none"
            >
                <option value="title">По названию</option>
                <option value="price">По цене (Дешевые)</option>
                <option value="-price">По цене (Дорогие)</option>
            </select>

            <div class="relative">
                <img
                    class="absolute left-4 top-3"
                    src="/search.svg"
                    alt="Search"
                />
                <input
                    @input="onChangeSearchInput"
                    type="text"
                    placeholder="Поиск..."
                    class="border rounded-md py-2 pl-12 pr-4 outline-none focus:border-slate-400"
                />
            </div>
        </div>
    </div>

    <div class="mt-10">
        <CardList
            :items="items"
            @add-to-favorites="addToFavorites"
            @add-to-cart="onClickAddPlus"
        />
    </div>
</template>
