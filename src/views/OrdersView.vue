<template>
  <div>
    <div class="main-container">
      <h1>Orders Management:</h1>
      <Dashboard />
    </div>
  </div>
</template>

<script>
  import Dashboard from '../components/Dashboard'

  export default {
    name: 'Orders',
    components: {
      Dashboard
    }
  }
</script>