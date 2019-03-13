<template>
  <div class="home">
    <ModelsViewer/>
  </div>
</template>

<script>
// @ is an alias to /src
import ModelsViewer from '@/components/ModelsViewer.vue'

export default {
  name: 'home',
  components: {
      ModelsViewer
  }
}
</script>
