<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

const searchQuery = ref('')
const destinations = ref([])
const tours = ref([])
const loading = ref(true)

onMounted(async () => {
  try {
    const [destRes, tourRes] = await Promise.all([
      axios.get('http://localhost:3001/destinations'),
      axios.get('http://localhost:3001/tours')
    ])
    destinations.value = destRes.data
    tours.value = tourRes.data
  } catch (err) {
    console.error('API error:', err)
  } finally {
    loading.value = false
  }
})

const filteredDestinations = computed(() => {
  if (!searchQuery.value) return destinations.value
  return destinations.value.filter(d =>
    d.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})

const filteredTours = computed(() => {
  if (!searchQuery.value) return tours.value
  return tours.value.filter(t =>
    t.title.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
})
</script>

<template>
  <header>
    <div class="navbar">
      <div class="logo" @click="$router.push('/')" style="cursor: pointer;">TourBooker</div>
      <div class="search-bar">
        <input v-model="searchQuery" type="text" placeholder="Where do you want to go?">
        <i class="fas fa-search"></i>
      </div>
      <div class="nav-links">
        <router-link to="/my-bookings">My Bookings</router-link>
        <a href="#">Sign in</a>
        <a href="#">Help</a>
      </div>
    </div>
  </header>

  <section class="hero">
    <h1>Find your next unforgettable experience</h1>
    <p>Tours, attractions, and activities in over 100 countries — all in one place.</p>
  </section>

  <section class="section">
    <h2 class="section-title">Popular Destinations</h2>
    <div class="grid">
      <div v-for="dest in filteredDestinations" :key="dest.id" class="card">
        <img :src="dest.image" :alt="dest.name">
        <div class="card-content">
          <h3 class="card-title">{{ dest.name }}</h3>
          <p>{{ dest.experiences }} experiences</p>
        </div>
      </div>
    </div>
  </section>

  <section class="section" style="background: var(--light-gray);">
    <h2 class="section-title">Top Experiences Around the World</h2>
    <div class="grid">
      <div v-for="tour in filteredTours" 
            :key="tour.id" 
            class="card"
            @click="$router.push(`/tour/${tour.id}`)"
            style="cursor: pointer;">
            <img :src="tour.image" :alt="tour.title">
        <div class="card-content">
          <h3 class="card-title">{{ tour.title }}</h3>
          <div class="rating">
            <i class="fas fa-star"></i> {{ tour.rating }} ({{ tour.reviews }} reviews)
          </div>
          <p>{{ tour.description }}</p>
          <div style="display:flex; justify-content:space-between; align-items:end;">
            <div>
              <span style="font-size:0.9rem; color:var(--gray);">From</span><br>
              <span class="price">${{ tour.price }}</span>
            </div>
            <button class="btn">Book now</button>
          </div>
        </div>
      </div>
    </div>
  </section>

  <footer>
    <p>&copy; 2025 TourBooker. Made with ❤️ for travelers.</p>
    <p style="margin-top:10px; font-size:0.9rem;">Inspired by GetYourGuide • Terms • Privacy • Contact</p>
  </footer>
</template>