<script lang="ts" setup>
import { computed, ref, watch } from 'vue';

  interface Header {
      text: string,
      value: string,
      align?: string,
      width?: string
  }

  const props = withDefaults(defineProps<{
      headers: Header[] ,
      items : any[],
      title?: string,
      loading?: boolean,
      primaryColor?: string,
      search?: boolean,
      queries?: {[key:string]:any}
  }>(), {
    loading: false,
    title: '',
    primaryColor: 'rgb(5, 114, 206)',
    search: true,
    queries: ()=> {return{page: 1}}
  })

  const emits = defineEmits(['update:queries'])


  /// Queries ///
  const dataQueries = computed({
    get() {
      return props.queries
    },
    set(val){
      emits('update:queries', val)
    }
  })

  /// Search Debounce ///
  let debounce: ReturnType<typeof setTimeout>
  const debounceSearch = ()=>{
      debounce ? clearTimeout(debounce) : null
      debounce = setTimeout(() => {
        dataQueries.value.search = mySearch.value
      }, 800)
    }
    const mySearch = ref(dataQueries.value.search)
    watch(mySearch, (val:string, old: string) => {
      if (val || (val === '' && old)) {
        dataQueries.value.page = 1
        debounceSearch()
      }
    })

</script>
<template>
  <div class="s-card">
    <div class="s-header">
      <div class="s-title">
        <slot name="title">
          <h4>{{ title }}</h4>
        </slot>
      </div>
      <div v-if="search" class="search">
        <input
          id="input"
          v-model="mySearch"
          type="text"
          class="search-text"
          placeholder="Search"
        >
        <label for="input" class="search-label">Search</label>
      </div>
    </div>
    <div v-if="loading" class="progress-bar">
      <div class="progress-bar-value" :style="`background-color: ${primaryColor};`" />
    </div>
    <table class="s-table">
      <thead>
        <tr>
          <th v-for="th in props.headers" :key="th.value" :style="`text-align: ${th.align}; width: ${th.width}`">
            {{ th.text }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="item in props.items"
          :key="item.id"
        >
          <td v-for="td in headers" :key="td.value" :style="`text-align: ${td.align}; width: ${td.width}`">
            <slot :name="td.value" :item="item">
              {{ item[td.value] }}
            </slot>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<style>
.s-card {
  background-color: white;
  border-radius: 5px;
  overflow-x: scroll;
  -ms-overflow-style: none; /* for Internet Explorer, Edge */
  scrollbar-width: none; /* for Firefox */
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
  padding: 5px 10px;
}
.s-card::-webkit-scrollbar {
    display: none; /* for Chrome, Safari, and Opera */
}
.s-card .s-title {
  padding: 5px;
}
.s-table {
    width: 100%;
    border-collapse: collapse;
    margin: 1px 0;
    font-size: 0.9em;
    min-width: 400px;
    
}
.s-table thead tr {
    /* background-color: #009879; */
    color: rgba(0,0,0,.6);
    user-select: none;
    font-size: .75rem;
    height: 48px;
    text-align: left;
    border-bottom: 1px solid #dddddd;
}
.s-table th,
.s-table td {
    padding: 12px 15px;
}
.s-table tbody tr {
    border-bottom: 1px solid #dddddd;
}

/* .s-table tbody tr:nth-of-type(even) {
    background-color: #f3f3f3;
} */

/* .s-table tbody tr:last-of-type {
    border-bottom: 2px solid #009879;
} */
.s-table tbody tr.active-row {
    font-weight: bold;
    color: #009879;
}
/* progress bar */
.progress-bar {
  height: 4px;
  background-color: rgba(13, 118, 192, 0.2);
  width: 100%;
  overflow: hidden;
}

.progress-bar-value {
  width: 100%;
  height: 100%;
  animation: indeterminateAnimation 1s infinite linear;
  transform-origin: 0% 50%;
}

@keyframes indeterminateAnimation {
  0% {
    transform:  translateX(0) scaleX(0);
  }
  40% {
    transform:  translateX(0) scaleX(0.4);
  }
  100% {
    transform:  translateX(100%) scaleX(0.5);
  }
}
/* */
/* Search */
.search {
  position: relative;
}
.search-text {
  display: block;
  margin: 5px 0;
  padding: 0.5rem 1.6rem;
  color: inherit;
  width: 50%;
  font-family: inherit;
  font-size: 1rem;
  font-weight: inherit;
  line-height: 1.2;
  border: solid 1px grey;
  border-radius: 0.4rem;
  transition: box-shadow 300ms;
}

.search-text::placeholder {
  color: #B0BEC5;
}

.search-text:focus {
  outline: none;
  box-shadow: 1px 1px 5px #a5a4d0;
}

.search-label {
  display: block;
  position: absolute;
  bottom: 95%;
  background: white;
  left: 1rem;
  color: rgb(33, 29, 29);
  font-family: inherit;
  font-size: 15px;
  font-weight: inherit;
  line-height: 1.8;
  opacity: 0;
  transform: 
    translate3d(0, 50%, 0)
    scale(1);
  transform-origin: 0 0;
  transition:
    opacity 150ms cubic-bezier(0.645, 0.045, 0.355, 1),
    transform 150ms cubic-bezier(0.645, 0.045, 0.355, 1),
    visibility 0ms 150ms cubic-bezier(0.645, 0.045, 0.355, 1),
    z-index 0ms 150ms cubic-bezier(0.645, 0.045, 0.355, 1);
}

.search-text:placeholder-shown + .search-label {
  visibility: hidden;
  z-index: -1;
}

.search-text:not(:placeholder-shown) + .search-label,
.search-text:focus:not(:placeholder-shown) + .search-label {
  visibility: visible;
  z-index: 1;
  opacity: 1;
  transform:
    translate3d(0, 50%, 0)
    scale(0.8);
  transition:
    transform 300ms,
    visibility 0ms,
    z-index 0ms;
}
</style>