<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const bookings = ref([])
const loading = ref(true)

onMounted(async () => {
  try {
    const res = await axios.get('http://localhost:3001/bookings')
    // Sort by most recent first
    bookings.value = res.data.sort((a, b) => new Date(b.bookedAt) - new Date(a.bookedAt))
  } catch (err) {
    console.error('Failed to load bookings:', err)
    bookings.value = []
  } finally {
    loading.value = false
  }
})
</script>

<template>
  <div class="my-bookings-page">
    <h1 class="page-title">My Bookings</h1>

    <div v-if="loading" class="loading">Loading your bookings...</div>

    <div v-else-if="bookings.length === 0" class="empty-state">
      <i class="fas fa-ticket-alt" style="font-size: 4rem; color: var(--gray); margin-bottom: 20px;"></i>
      <h2>No bookings yet</h2>
      <p>Start exploring tours and make your first booking!</p>
      <router-link to="/" class="btn">Browse Tours</router-link>
    </div>

    <div v-else class="bookings-list">
      <div v-for="booking in bookings" :key="booking.id" class="booking-card">
        <div class="booking-header">
          <h3>{{ booking.tourTitle }}</h3>
          <span class="status">Confirmed</span>
        </div>

        <div class="booking-details">
          <div class="detail-item">
            <i class="fas fa-calendar"></i>
            <span><strong>Date:</strong> {{ new Date(booking.date).toLocaleDateString() }}</span>
          </div>
          <div class="detail-item">
            <i class="fas fa-users"></i>
            <span><strong>Guests:</strong> {{ booking.adults }} Adult{{ booking.adults > 1 ? 's' : '' }}
              {{ booking.children > 0 ? `, ${booking.children} Child${booking.children > 1 ? 'ren' : ''}` : '' }}
            </span>
          </div>
          <div class="detail-item">
            <i class="fas fa-clock"></i>
            <span><strong>Booked on:</strong> {{ new Date(booking.bookedAt).toLocaleDateString() }}</span>
          </div>
        </div>

        <div class="booking-price">
          <span>Total paid</span>
          <strong>${{ booking.totalPrice }}</strong>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.my-bookings-page {
  max-width: 1000px;
  margin: 0 auto;
  padding: 40px 5%;
}
.page-title {
  font-size: 2.8rem;
  text-align: center;
  margin-bottom: 40px;
  color: var(--navy);
}
.loading {
  text-align: center;
  padding: 60px;
  font-size: 1.5rem;
  color: var(--gray);
}
.empty-state {
  text-align: center;
  padding: 80px 20px;
  color: var(--gray);
}
.empty-state h2 {
  margin: 20px 0;
}
.bookings-list {
  display: grid;
  gap: 24px;
}
.booking-card {
  background: white;
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.08);
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 20px;
  align-items: center;
}
.booking-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.booking-header h3 {
  margin: 0;
  font-size: 1.4rem;
}
.status {
  background: #e6f7ee;
  color: #28a745;
  padding: 6px 12px;
  border-radius: 20px;
  font-size: 0.9rem;
  font-weight: 600;
}
.booking-details {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.detail-item {
  display: flex;
  align-items: center;
  gap: 10px;
  color: var(--gray);
}
.detail-item i {
  color: var(--orange);
  width: 20px;
}
.booking-price {
  text-align: right;
}
.booking-price span {
  display: block;
  color: var(--gray);
  font-size: 0.9rem;
}
.booking-price strong {
  font-size: 1.8rem;
  color: var(--navy);
}
@media (max-width: 768px) {
  .booking-card {
    grid-template-columns: 1fr;
    text-align: center;
  }
  .booking-price {
    text-align: center;
    margin-top: 20px;
  }
}
</style>