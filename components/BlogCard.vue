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
  domain: '',
  favicon: '',
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
  const domain = new URL(props.url).hostname 

  console.log({ domain, title, description, image })
  data.title = title
  data.description = description
  data.image = image
  data.domain = domain
  data.favicon = `https://www.google.com/s2/favicons?sz=14&domain=${encodeURI(props.url)}`
})
</script>
<template>
  <div>
    <div class="card">
      <img class="card-image" v-if="data.image" :src="data.image" alt="画像">
      <div class="card-content">
        <h2>{{ data.title }}</h2>
        <p v-if="data.description">{{ data.description }}</p>
        <a :href="url"><img class="card-favicon" :src="data.favicon" />{{ data.domain }}</a>
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

.card-image {
  width: 200px;
  height: 200px;
}

.card-favicon {
  width: 14px;
  height: 14px;
  float: left;
  margin-top: 5px;
  margin-right: 5px;
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