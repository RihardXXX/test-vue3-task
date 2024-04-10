<template>
    <section :style="`background: ${color}`" >
        <v-select
            v-model="selectSort"
            :items="typeSort"
            label="Сортировка"
            item-title="title"
            item-value="value"
        ></v-select>
    </section>
</template>

<script setup>
import {ref, toRefs, watch, defineEmits, toRaw} from 'vue'

const props = defineProps({
    options: {},
})

const { color, id } = toRefs(props.options)

const emit = defineEmits(['toSorted'])

const typeSort = ref([
    { title: 'по возрастанию', value: 'asc' },
    { title: 'по убыванию', value: 'desc' },
    { title: 'без сортировки', value: 'none' }
])

const selectSort = ref('none')

watch(selectSort,() => {
    callSorted(selectSort.value)
})

function callSorted(typeSort) {
    emit('toSorted', { type: typeSort, column: id.value })
}

</script>

<style lang="scss" scoped>
section {
    padding: 10px;
    width: 400px;
    //min-height: 100px;
    height: fit-content;
    border-radius: 10px;
    display: flex;
    //flex-direction: column;
    align-items: center;
}
</style>