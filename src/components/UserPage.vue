<template>
  <div style="margin-top: 20px;">
    <h2>User: {{ $route.params.username }}</h2>
    <input
      v-model="search"
      placeholder="Type search query"
      style="padding: 8px; width: 300px;"
    />
    <p>Current stored query for this page: "{{ currentStoredQuery }}"</p>
  </div>
</template>

<script>
// ✅ Global in-memory store (module-level variable)
const queriesByRoute = {}

const STORAGE_KEY = 'atlassian.search-dialog-query'

export default {
  data() {
    return {
      search: ''
    }
  },

  computed: {
    routeKey() {
      return this.$route.fullPath
    },
    currentStoredQuery() {
      // ✅ Make it accessible to template
      return queriesByRoute[this.routeKey] || ''
    }
  },

  watch: {
    search(newVal) {
      queriesByRoute[this.routeKey] = newVal
      sessionStorage.setItem(STORAGE_KEY, newVal)
    },

    '$route.fullPath': {
      immediate: true,
      handler(newVal) {
        if (queriesByRoute[newVal]) {
          this.search = queriesByRoute[newVal]
        } else {
          this.search = sessionStorage.getItem(STORAGE_KEY) || ''
          queriesByRoute[newVal] = this.search
        }
      }
    }
  }
}
</script>
