<template>
  <AWLHeader />
  <div>
    <h1>Leaderboard</h1>
    <table>
      <thead>
        <tr>
          <th>Username</th>
          <th>Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="entry in entries" :key="entry.userId">
          <td>{{ entry.userId }}</td>
          <td>{{ formatTime(entry.total) }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { ref, onMounted } from 'vue'
import { getLeaderboard } from '@/firebase/data'
import { type ScreenTimeSummary } from '@/types'
import AWLHeader from '@/components/Header.vue'

export default {
  name: 'AWLLeaderboard',
  setup() {
    const entries = ref([] as ScreenTimeSummary[])

    onMounted(async () => {
      try {
        const data = (await getLeaderboard()) as ScreenTimeSummary[]
        data.sort((a, b) => b.total - a.total)
        entries.value = data
      } catch (error) {
        console.error(error)
      }
    })

    // Helper method to format time in minutes and hours
    const formatTime = (milliseconds: number) => {
      const hours = Math.floor(milliseconds / 60000)
      const minutes = milliseconds % 60
      return `${hours}h ${minutes}m`
    }

    return { entries, formatTime }
  },
  components: {
    AWLHeader
  }
}
</script>

<style scoped>
table {
  width: 100%;
}

table,
th,
td {
  border: 1px solid black;
  border-collapse: collapse;
}

th,
td {
  padding: 15px;
  text-align: left;
}
</style>
