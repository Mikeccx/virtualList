<template>
  <div class="container">
    <virtualList :items="list" />
  </div>
</template>

<script>
import virtualList from '@/components/virtualList'
// 模拟一个长列表
const list = []
for(let i=0; i<10000; i++) {
  list.push({
    id: i,
    label: `virtual-list ${i}`
  })
}
export default {
  components: {
    virtualList
  },
  data() {
    return {
      list: list
    }
  }
}
</script>

<style>
html, body {
    height: 100%;
}
.container {
  height: 100%;
  border: 1px solid #ccc;
}
</style>