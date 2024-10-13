<template>
  <div>
    <div class="flex items-center p-2 border-b border-gray-300 last:border-b-0 gap-4">
      <input type="checkbox" v-model="isChecked" @change="handleCheck" />
      <a :href="'https://www.klerk.ru' + rubric.url" target="_blank"
        >{{ rubric.title }} ({{ rubric.count }}, {{ totalChildCount }})</a
      >
      <button
        class="items-center text-white line-clamp-1 justify-center ml-2 w-5 h-5 rounded-sm bg-slate-400 border border-slate-400 hover:bg-slate-500"
        :style="rubric.children.length ? 'display: flex' : 'display: none'"
        @click="toggleChildren"
      >
        {{ showChildren ? '-' : '+' }}
      </button>
    </div>
    <div v-if="showChildren" class="ml-6">
      <tree-item
        v-for="child in rubric.children"
        :key="child.id"
        :rubric="child"
        @update-count="emit('update-count', $event)"
        :show-empty="showEmpty"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
const props = defineProps({
  rubric: Object,
  showEmpty: Boolean
})

const emit = defineEmits(['update-count'])

const showChildren = ref(false)
const isChecked = ref(false)

const totalChildCount = computed(() => {
  let count = props.rubric.count
  props.rubric.children.forEach((child) => {
    count += child.count
  })
  return count
})

function handleCheck() {
  const delta = isChecked.value ? totalChildCount.value : -totalChildCount.value
  emit('update-count', delta)
}
function toggleChildren() {
  showChildren.value = !showChildren.value
}
</script>
<style>
a:hover {
  color: rgb(44, 124, 236);
}
</style>
