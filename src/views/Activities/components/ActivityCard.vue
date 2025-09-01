<script setup>
import { ref, computed } from "vue";

// Props
const props = defineProps({
  title: {
    type: String,
    required: true,
  },
  events: {
    type: Array,
    required: true,
  },
});

// 分頁邏輯
const currentPage = ref(0);
const itemsPerPage = 5;

const paginatedEvents = computed(() => {
  const start = currentPage.value * itemsPerPage;
  const end = start + itemsPerPage;
  return props.events.slice(start, end);
});

const totalPages = computed(() => Math.ceil(props.events.length / itemsPerPage));

function nextPage() {
  if (currentPage.value < totalPages.value - 1) {
    currentPage.value++;
  }
}

function prevPage() {
  if (currentPage.value > 0) {
    currentPage.value--;
  }
}
</script>

<template>
  <div class="card p-3 mb-2">
    <h6 class="mb-2">{{ title }}</h6>
    <div class="event-list">
      <div v-for="group in paginatedEvents" :key="group.date" class="mb-3">
        <div class="fw-bold small mb-1">{{ group.date }}</div>
        <ul class="list-unstyled mb-0">
          <li v-for="ev in group.events" :key="ev.id"
            class="d-flex justify-content-between align-items-start py-1 border-bottom">
            <div>
              <div class="fw-bold">
                <template v-if="ev.url">
                  <a :href="ev.url" target="_blank" rel="noopener" class="text-primary link-like">{{ ev.title }}</a>
                </template>
                <template v-else>
                  <a href="#" @click.prevent="$emit('openDay', ev.date)" class="text-reset">{{ ev.title }}</a>
                </template>
              </div>
              <div class="text-sm text-muted">
                {{ ev.time }} • {{ ev.location }}
              </div>
            </div>
            <span class="badge bg-gradient-primary">{{ ev.tag }}</span>
          </li>
        </ul>
      </div>
      <div v-if="props.events.length === 0" class="text-muted">
        無活動
      </div>
      <div v-if="props.events.length > 0" class="d-flex justify-content-center mt-3">
        <button class="btn btn-sm btn-outline-primary me-2" @click="prevPage" :disabled="currentPage === 0">上一頁</button>
        <span class="align-self-center">{{ currentPage + 1 }} / {{ totalPages }}</span>
        <button class="btn btn-sm btn-outline-primary ms-2" @click="nextPage" :disabled="currentPage >= totalPages - 1">下一頁</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.event-list {
  overflow: hidden;
}

.event-list .fw-bold {
  font-size: 0.95rem;
}

/* link-like: green by default, red on hover */
.link-like {
  color: var(--bs-success, #28a745);
  cursor: pointer;
  text-decoration: none;
}

.link-like:hover {
  color: var(--bs-danger, #dc3545);
  text-decoration: underline;
}
</style>
