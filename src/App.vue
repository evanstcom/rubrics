<template>
  <div class="p-16">
    <h1 class="text-2xl font-bold mb-6">klerk</h1>
    <div>
      <div class="text-lg mb-4">
        <input class="default:ring-2" type="checkbox" v-model="showEmpty" @change="fetchRubrics" />
        Отображать пустые рубрики
      </div>
      <div class="text-md">Сумма отмеченных: {{ totalCount }}</div>
      <tree-item
        v-for="rubric in rubrics"
        :key="rubric.id"
        :rubric="rubric"
        @update-count="updateTotalCount"
        :show-empty="showEmpty"
      />
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import TreeItem from './components/TreeItem.vue' // Рекурсивный компонент
import { onMounted, ref } from 'vue'

const rubrics = ref([])
const showEmpty = ref(false)
const totalCount = ref(0)

function fetchRubrics() {
  const url = `https://www.klerk.ru/yindex.php/v3/event/rubrics?allowEmpty=${showEmpty.value ? 1 : 0}`
  axios.get(url).then((response) => {
    rubrics.value = transformRubrics(response.data)
  })
}
function transformRubrics(data) {
  // Преобразовать данные в нужную структуру
  return data.map((item) => ({
    ...item,
    children: item.children ? transformRubrics(item.children) : []
  }))
}

function updateTotalCount(delta) {
  totalCount.value += delta
}

onMounted(() => {
  fetchRubrics()
})
</script>
