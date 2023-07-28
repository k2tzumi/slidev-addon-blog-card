<script setup lang="ts">
import { Vue, reactive, onMounted } from 'vue'
import { VueJsonp, jsonp } from 'vue-jsonp'

Vue.use(VueJsonp)

interface Props {
  url: String
}

const props = defineProps<Props>()

const data = reactive({
    title: '',
    description: '',
    image: '',
})

onMounted(async () => {
    // URLからHTMLを取得してOGPタグを抽出

    const html = await jsonp<string>( props.url)
    const parser = new DOMParser()
    const doc = parser.parseFromString(html, 'text/html')
    data.title = doc.querySelector('meta[property="og:title"]').content
    data.description = doc.querySelector('meta[property="og:description"]').content
    data.image = doc.querySelector('meta[property="og:image"]').content
})
</script>
<template>
  <div>
    <div class="card">
      <img v-if="image" :src="image" alt="画像">
      <div class="card-content">
        <h2>{{ title }}</h2>
        <p v-if="description">{{ description }}</p>
        <a :href="url">{{ url }}</a>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  display: flex;
  align-items: center;
  border: 1px solid #ccc;
  margin: 10px;
}

.card img {
  width: 200px;
  height: 200px;
}

.card-content {
  padding: 10px;
}

.card-content h2 {
  font-size: 18px;
}

.card-content p {
  font-size: 14px;
}

.card-content a {
  color: blue;
}
</style>C