<script setup>
import DrawerHead from "./DrawerHead.vue";
import CartListItem from "./CartListItem.vue";
import InfoBlock from "./InfoBlock.vue";

const emit = defineEmits(["createOrder"]);

const props = defineProps({
    totalPrice: Number,
    vatPrice: Number,
    buttonDisabled: Boolean,
});
</script>
<template>
    <div
        class="fixed top-0 left-0 w-full h-full bg-black z-10 opacity-70"
    ></div>
    <div class="fixed right-0 top-0 bg-white w-96 h-full z-20 p-8">
        <DrawerHead />

        <div v-if="!totalPrice" class="flex justify-center h-full items-center">
            <InfoBlock
                title="Корзина пустая"
                description="Добавьте хотя бы 1 предмет"
                image-url="/package-icon.png"
            />
        </div>
        <div v-else>
            <CartListItem />

            <div class="flex flex-col gap-4 mb-5 my-7">
                <div class="flex gap-2">
                    <span>Итого:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>{{ totalPrice }} руб.</b>
                </div>

                <div class="flex gap-2">
                    <span>Налог 5%:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>{{ vatPrice }} руб.</b>
                </div>

                <button
                    :disabled="buttonDisabled"
                    @click="() => emit('createOrder')"
                    class="mt-4 bg-lime-500 w-full rounded-xl py-3 disabled:bg-slate-400 text-white hover:bg-lime-600 transition active:bg-lime-700 cursor-pointer"
                >
                    Оформить заказ
                </button>
            </div>
        </div>
    </div>
</template>
