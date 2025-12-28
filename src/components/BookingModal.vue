<script setup>
import { ref, computed, defineEmits } from 'vue'
import axios from 'axios'

const props = defineProps({
  tour: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['close'])

const selectedDate = ref('')
const adults = ref(1)
const children = ref(0)
const isSubmitting = ref(false)
const success = ref(false)

const totalPrice = computed(() => {
  const adultPrice = props.tour.price * adults.value
  const childPrice = props.tour.price * 0.7 * children.value // 30% discount for kids
  return Math.round(adultPrice + childPrice)
})

const submitBooking = async () => {
  if (!selectedDate.value) {
    alert('Please select a date!')
    return
  }

  isSubmitting.value = true

  const bookingData = {
    tourId: props.tour.id,
    tourTitle: props.tour.title,
    date: selectedDate.value,
    adults: adults.value,
    children: children.value,
    totalPrice: totalPrice.value,
    bookedAt: new Date().toISOString()
  }

  try {
    await axios.post('http://localhost:3001/bookings', bookingData)
    success.value = true
  } catch (err) {
    alert('Booking failed. Please try again.')
    console.error(err)
  } finally {
    isSubmitting.value = false
  }
}

const closeModal = () => {
  emit('close')
}
</script>

<template>
  <div class="modal-overlay" @click="closeModal">
    <div class="modal" @click.stop>
      <button class="close-btn" @click="closeModal">&times;</button>

      <h2 v-if="!success">Book: {{ tour.title }}</h2>
      <div v-if="success" class="success-message">
        <i class="fas fa-check-circle" style="font-size: 4rem; color: green; margin-bottom: 20px;"></i>
        <h2>Booking Confirmed!</h2>
        <p>Thank you! We'll send a confirmation to your email.</p>
        <button class="btn" @click="closeModal" style="margin-top: 20px;">Close</button>
      </div>

      <div v-else>
        <div class="modal-image">
          <img :src="tour.image" :alt="tour.title">
        </div>

        <div class="form-group">
          <label>Select Date</label>
          <input type="date" v-model="selectedDate" :min="new Date().toISOString().split('T')[0]" required />
        </div>

        <div class="form-group">
          <label>Adults</label>
          <select v-model="adults">
            <option v-for="n in 10" :key="n" :value="n">{{ n }}</option>
          </select>
        </div>

        <div class="form-group">
          <label>Children (30% off)</label>
          <select v-model="children">
            <option v-for="n in 11" :key="n" :value="n - 1">{{ n - 1 }}</option>
          </select>
        </div>

        <div class="price-summary">
          <div>Total</div>
          <div class="total-price">${{ totalPrice }}</div>
        </div>

        <button 
          class="btn book-btn" 
          @click="submitBooking" 
          :disabled="isSubmitting"
        >
          {{ isSubmitting ? 'Processing...' : 'Confirm & Pay' }}
        </button>

        <p class="secure">🔒 Secure payment • Free cancellation</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0,0,0,0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}
.modal {
  background: white;
  border-radius: 16px;
  width: 90%;
  max-width: 500px;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  padding: 30px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.2);
}
.close-btn {
  position: absolute;
  top: 15px;
  right: 20px;
  font-size: 2rem;
  background: none;
  border: none;
  cursor: pointer;
  color: var(--gray);
}
.modal-image img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 12px;
  margin-bottom: 20px;
}
.form-group {
  margin-bottom: 20px;
}
.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
}
.form-group input, .form-group select {
  width: 100%;
  padding: 12px;
  border: 1px solid var(--border);
  border-radius: 8px;
  font-size: 1rem;
}
.price-summary {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 0;
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
  margin: 20px 0;
  font-size: 1.4rem;
  font-weight: 700;
}
.total-price {
  color: var(--navy);
  font-size: 2rem;
}
.book-btn {
  width: 100%;
  padding: 16px;
  font-size: 1.2rem;
}
.secure {
  text-align: center;
  margin-top: 20px;
  color: var(--gray);
  font-size: 0.9rem;
}
.success-message {
  text-align: center;
  padding: 40px 20px;
}
</style>