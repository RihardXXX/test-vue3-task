<template>
  <main>
    <div class="wrapper mt-2 mb-0">
      <TheFilterSort v-for="filter in CardListOptions"
                     :key="filter.id"
                     :options="filter"
                     class="mb-10"
                     @toSorted="sortByColumns"
      />
    </div>
    <div class="wrapper">
        <CardList v-for="(item, index) in CardListOptions"
                  :key="index"
                  :options="item"
        />
    </div>
  </main>
</template>

<script setup>
  import { ref, provide } from 'vue';
  import axios from 'axios';
  import CardList from './components/CardList.vue';
  import TheFilterSort from "./components/TheFilterSort.vue";

  const firstList = ref([]);
  const secondList = ref([]);
  const lastList = ref([]);

  const CardListOptions = ref([
    {
      color: '#0aa6e362',
      title: 'Необработанные',
      id: 1,
    },
    {
      color: '#e86405bd',
      title: 'В работе',
      id: 2,
    },
    {
      color: '#9400d364',
      title: 'Завершенные',
      id: 3,
    },
  ]);

  const getAllCards = async () => {
    try {
      await axios
        .get('https://fakestoreapi.com/products')
        .then((res) => (firstList.value = res.data));
      console.log('getAllCards - успешно');
    } catch (error) {
      console.log(error);
      alert('Упс! Ошибка получения данных ;(');
    }
  };

  getAllCards();

  provide('firstList', firstList);
  provide('secondList', secondList);
  provide('lastList', lastList);

  const asc = (a, b) =>  a.rating.rate - b.rating.rate
  const desc = (a, b) => b.rating.rate - a.rating.rate

  // игнор сортировки
  const noSort = (a, b) => 0;

  // перетасовка после сортировок по убыв и возрастанию
  const shuffle = (array) => {
      let result = [...array]
      for (let i = result.length - 1; i > 0; i--) {
          const j = Math.floor(Math.random() * (i + 1));
          [result[i], result[j]] = [result[j], result[i]];
      }
      return result
  }

  // функция определяет какой тип сортировки применить
  // по возрастанию и убыванию
  const pattern = {
      asc,
      desc,
  }
  const selectTypeSort = (typeSort) => pattern[typeSort]

  // вызовы разных типов сортировок
  const selectSort = (list, type) => {
      list.value = type !== 'none'
          ? list.value.toSorted(selectTypeSort(type))
          : shuffle(list.value)
  }

  function sortByColumns({ type, column }) {
      switch (column) {
          case 1:
              selectSort(firstList, type)
              break;
          case 2:
              selectSort(secondList, type)
              break;
          case 3:
              selectSort(lastList, type)
              break;
      }
  }
</script>

<style scoped>
  .wrapper {
    margin: .5% 0;
    display: flex;
    justify-content: center;
    gap: 5%;
    width: 100%;
  }
</style>
