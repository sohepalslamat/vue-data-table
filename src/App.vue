<script setup lang="ts">
  import axios from 'axios'
  import { reactive, ref } from 'vue';
  import DataTable from './lib/DataTable.vue';

  const headers = reactive([
    { text: 'id', align: 'center', width: '10px', value: 'id' },
    { text: 'Name', value: 'name' },
    { text: 'Price', value: 'price' },
    { text: 'Action', align: 'center', value: 'action' }
  ])
  const books = ref()
  axios.get('https://books.v2202107122785158474.goodsrv.de/books/').then((res:any)=>{
    books.value = res.data

  })


</script>

<template>
  <header>
    
  </header>

  <main>
    <DataTable 
      v-if="books"
      :headers="headers"
      :items="books"
    >
    <template #title>
      <h3>
        Books
      </h3>
    </template>
    
  </DataTable>
  </main>
</template>

<style scoped>
</style>
