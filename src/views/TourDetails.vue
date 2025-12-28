<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import axios from 'axios'
import BookingModal from '../components/BookingModal.vue'

const route = useRoute()
const router = useRouter()
const tour = ref(null)
const loading = ref(true)
const showBookingModal = ref(false)

onMounted(async () => {
  const id = route.params.id
  try {
    const res = await axios.get(`http://localhost:3001/tours/${id}`)
    tour.value = res.data
  } catch (err) {
    console.error('Tour not found')
    router.push('/') // Redirect home if not found
  } finally {
    loading.value = false
  }
})
</script>

<template>
  <div v-if="loading" class="loading">Loading tour details...</div>
  
  <div v-else-if="tour" class="tour-details">
    <button @click="router.push('/')" class="back-btn">
      <i class="fas fa-arrow-left"></i> Back to home
    </button>
    
    <div class="hero-image">
      <img :src="tour.image" :alt="tour.title">
    </div>
    
    <div class="details-container">
      <div class="main-info">
        <h1>{{ tour.title }}</h1>
        
        <div class="rating">
          <i class="fas fa-star"></i>
          <strong>{{ tour.rating }}</strong> ({{ tour.reviews }} reviews)
        </div>
        
        <div class="highlights">
          <ul>
            <li>Skip-the-line access</li>
            <li>Professional local guide</li>
            <li>Small group experience</li>
            <li>Free cancellation up to 24 hours</li>
          </ul>
        </div>
        
        <div class="description">
          <h2>About this activity</h2>
          <p>
            Experience one of the world's most iconic landmarks with exclusive summit access.
            Enjoy breathtaking panoramic views of Paris from the top of the Eiffel Tower.
            Your expert guide will share fascinating stories and history along the way.
          </p>
        </div>
        
        <div class="included">
          <h2>What's included</h2>
          <ul class="check-list">
            <li><i class="fas fa-check"></i> Summit elevator access</li>
            <li><i class="fas fa-check"></i> Guided tour in English</li>
            <li><i class="fas fa-check"></i> Small group (max 20 people)</li>
          </ul>
          
          <h2 style="margin-top: 2rem;">Not included</h2>
          <ul class="cross-list">
            <li><i class="fas fa-times"></i> Hotel pickup/drop-off</li>
            <li><i class="fas fa-times"></i> Food & drinks</li>
          </ul>
        </div>
        
        <div class="reviews">
          <h2>Customer reviews</h2>
          <div class="review-card">
            <strong>Amazing experience!</strong> ⭐⭐⭐⭐⭐
            <p>"Our guide was fantastic and the views from the summit were unforgettable!" – Sarah, USA</p>
          </div>
          <div class="review-card">
            <strong>Worth every penny</strong> ⭐⭐⭐⭐⭐
            <p>"Skipping the line saved hours. Highly recommend!" – Marco, Italy</p>
          </div>
        </div>
      </div>
      
      <div class="booking-sidebar">
  <div class="price-box">
    <span class="from">From</span>
    <div class="price">${{ tour ? tour.price : '' }}</div>
    <span class="per-person">per person</span>
  </div>
  <button 
    class="btn book-btn" 
    @click="showBookingModal = true"
    :disabled="loading"
  >
    Book now
  </button>
  <p class="guarantee">Free cancellation • Instant confirmation</p>
</div>

<!-- Booking Modal -->
<teleport to="body">
  <BookingModal 
    v-if="showBookingModal && tour"
    :tour="tour"
    @close="showBookingModal = false"
  />
</teleport>

    </div>
  </div>
</template>

<style scoped>
.loading {
  text-align: center;
  padding: 100px;
  font-size: 1.5rem;
}
.tour-details {
  max-width: 1400px;
  margin: 0 auto;
  padding: 20px 5%;
}
.back-btn {
  background: none;
  border: none;
  color: var(--navy);
  font-size: 1.1rem;
  cursor: pointer;
  margin-bottom: 20px;
}
.back-btn i { margin-right: 8px; }
.hero-image img {
  width: 100%;
  height: 500px;
  object-fit: cover;
  border-radius: 16px;
}
.details-container {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 40px;
  margin-top: 40px;
}
.main-info h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
}
.rating {
  color: var(--orange);
  font-size: 1.3rem;
  margin-bottom: 20px;
}
.highlights ul {
  list-style: disc;
  padding-left: 20px;
  color: var(--gray);
}
.description h2, .included h2, .reviews h2 {
  font-size: 1.8rem;
  margin: 40px 0 20px;
}
.check-list li, .cross-list li {
  margin: 10px 0;
}
.check-list i { color: green; margin-right: 10px; }
.cross-list i { color: red; margin-right: 10px; }
.review-card {
  background: var(--light-gray);
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 20px;
}
.booking-sidebar {
  position: sticky;
  top: 100px;
  align-self: start;
  background: white;
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.1);
  padding: 30px;
  text-align: center;
}
.price-box {
  margin-bottom: 30px;
}
.from {
  font-size: 1rem;
  color: var(--gray);
}
.price {
  font-size: 3rem;
  font-weight: 700;
  color: var(--navy);
}
.per-person {
  color: var(--gray);
}
.book-btn {
  width: 100%;
  padding: 16px;
  font-size: 1.3rem;
}
.guarantee {
  margin-top: 20px;
  color: var(--gray);
  font-size: 0.9rem;
}
@media (max-width: 992px) {
  .details-container {
    grid-template-columns: 1fr;
  }
  .booking-sidebar {
    position: static;
  }
}
</style>