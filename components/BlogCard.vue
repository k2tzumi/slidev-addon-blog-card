<script setup lang="ts">
import { reactive, onMounted } from 'vue'
import axios from 'axios'

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
  // Retrieve HTML from URL and extract OGP tags
  const response = await axios.get(props.url)
  const html = response.data
  const parser = new DOMParser()
  const doc = parser.parseFromString(html, 'text/html')
  const title = doc.querySelector('meta[property="og:title"]').content
  const description = doc.querySelector('meta[property="og:description"]').content
  const image = doc.querySelector('meta[property="og:image"]').content

  console.log({ title, description, image })
  data.title = title
  data.description = description
  data.image = image
})
</script>
<template>
  <div>
    <div class="card">
      <img v-if="data.image" :src="data.image" alt="画像">
      <div class="card-content">
        <h2>{{ data.title }}</h2>
        <p v-if="data.description">{{ data.description }}</p>
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
</style>