<template>
  <div class="home">
    <mainPage/>
  </div>
</template>

<script>
// @ is an alias to /src
import mainPage from '@/components/mainPage.vue'

export default {
  name: 'Home',
  components: {
    mainPage
  }
}
</script>
