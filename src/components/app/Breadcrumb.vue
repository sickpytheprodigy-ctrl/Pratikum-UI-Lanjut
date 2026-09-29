<script setup>
import { computed } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()

const breadcrumbs = computed(() => {
  const crumbs = route.matched
    .filter((m) => m.meta && m.meta.breadcrumb)
    .map((m) => ({
      name: m.name,
      // Route dinamis (mis. /browse/events/:id) diganti dengan path aktual
      path: m.path.includes(':') ? route.path : m.path,
      meta: m.meta,
    }))

  // Halaman detail event: sisipkan "Event List" sebelum crumb terakhir
  if (route.name === 'event-detail') {
    crumbs.splice(crumbs.length - 1, 0, {
      path: '/browse/events',
      meta: { breadcrumb: 'Event List' },
    })
  }

  // Pastikan "Home" selalu jadi crumb pertama
  if (crumbs.length === 0 || crumbs[0].meta.breadcrumb !== 'Home') {
    crumbs.unshift({
      path: '/',
      meta: { breadcrumb: 'Home' },
    })
  }

  return crumbs
})
</script>

<template>
  <nav class="breadcrumb" v-if="breadcrumbs.length > 0">
    <ul>
      <li v-for="(crumb, index) in breadcrumbs" :key="crumb.path">
        <span v-if="index > 0" class="separator">/</span>
        <router-link v-if="index < breadcrumbs.length - 1" :to="crumb.path">
          {{ crumb.meta.breadcrumb }}
        </router-link>
        <span v-else class="active-crumb">{{ crumb.meta.breadcrumb }}</span>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.breadcrumb {
  margin-bottom: 2rem;
  padding: 1rem;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 8px;
}
.breadcrumb ul {
  list-style: none;
  display: flex;
  padding: 0;
  margin: 0;
  align-items: center;
  gap: 0.5rem;
}
.breadcrumb a {
  text-decoration: none;
  color: var(--primary, #6644ff);
  font-weight: 500;
}
.breadcrumb a:hover {
  text-decoration: underline;
}
.separator {
  color: #888;
  margin: 0 0.5rem;
}
.active-crumb {
  color: #333;
  font-weight: 600;
}
@media (prefers-color-scheme: dark) {
  .active-crumb {
    color: #ccc;
  }
}
</style>