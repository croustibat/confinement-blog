<template>
  <div>
    <nuxt />

    <footer class="footer">
      <div class="content has-text-centered">
        <p></p>
      </div>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      navbarOpen: false
    }
  },
  methods: {
    toggleNavbar() {
      this.navbarOpen = !this.navbarOpen
    }
  }
}
</script>

<style>
nav.navbar {
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06) !important;
}
</style>
