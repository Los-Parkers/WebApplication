<script>
import VBaseLayout from '@/public/layout/base.layout.vue'
import VGoogleAutocomplete from '@/public/components/google-autocomplete.component.vue'
import VGoogleMap from '../components/google-map.component.vue'

import PvInputText from 'primevue/inputtext'
import PvInputMask from 'primevue/inputmask'
import PvButton from 'primevue/button'
import PvDivider from 'primevue/divider'

import { reactive, ref } from 'vue'
import { useRouter } from 'vue-router'
import useParkings from '@/store/useParkings'
import useAuth from '@/store/useAuth'

export default {
  name: 'register-park-page',
  components: {
    VBaseLayout,
    VGoogleAutocomplete,
    VGoogleMap,
    PvInputText,
    PvInputMask,
    PvButton,
    PvDivider
  },
  setup() {
    const MAP_DEFAULT_CENTER = { lat: -12.1061161, lng: -77.026921 }
    const MAP_DEFAULT_ZOOM = 18

    const router = useRouter()
    const parkingStore = useParkings()
    const authStore = useAuth()

    const map = ref(null)
    const formState = reactive({
      address: '',
      width: 0,
      length: 0,
      height: 0,
      price: 0,
      phone: '',
      description: '',
      latitude: 0,
      longitude: 0
    })
    const loading = ref(false)

    /** @param {google.maps.places.PlaceResult} place */
    const handleAutocompletePlaceChanged = (place) => {
      const {
        geometry: { location },
        formatted_address
      } = place

      formState.address = formatted_address

      if (!location) return
      map.value.panTo(location)
      map.value.setZoom(18)

      formState.latitude = location.lat()
      formState.longitude = location.lng()
    }

    const handlePostGarage = async () => {
      if (!formState.address) return

      loading.value = true
      try {
        await parkingStore.createParking({
          ...formState,
          price: +formState.price.replace(/[^\d.]/g, ''),
          userId: authStore.user.id
        })
        setTimeout(() => {
          router.push('/find-your-park')
        }, 1000)
      } catch (err) {
        console.error(err)
      } finally {
        loading.value = false
      }
    }
    return {
      MAP_DEFAULT_CENTER,
      MAP_DEFAULT_ZOOM,
      map,
      formState,
      loading,
      handleAutocompletePlaceChanged,
      handlePostGarage
    }
  }
}
</script>

<template>
  <v-base-layout>
    <section class="main-section">
      <div class="section-header">
        <h2 class="section-title">Rent your parking</h2>
        <p class="section-subtitle">
          Fill in the following form with the information of the parking lot you want to rent. Once
          you have completed the form, click on the "Post parking" button to publish the parking lot
        </p>
      </div>
      <pv-divider />
      <form @submit.prevent>
        <div class="register-form-section--top">
          <div class="register-form-field register-form-field--address">
            <label for="address">Exact address of the space to rent:</label>
            <v-google-autocomplete
              class="input-autocomplete"
              @placeChanged="handleAutocompletePlaceChanged"
              v-model="formState.address"
            />
          </div>
        </div>
        <div class="map-container">
          <v-google-map :center="MAP_DEFAULT_CENTER" :zoom="MAP_DEFAULT_ZOOM" v-model:map="map" />
        </div>
        <div class="register-form-section--bottom">
          <div class="register-form-additional-info">
            <div class="register-form-field">
              <label for="length">Length (m):</label>
              <pv-input-text v-model="formState.length" />
            </div>
            <div class="register-form-field">
              <label for="width">Width (m):</label>
              <pv-input-text v-model="formState.width" />
            </div>
            <div class="register-form-field">
              <label for="height">Height (m):</label>
              <pv-input-text v-model="formState.height" />
            </div>

            <div class="register-form-field">
              <label for="phone">Phone number</label>
              <pv-input-mask mask="999-999-999" v-model="formState.phone" />
            </div>
          </div>
          <div class="register-form-cta">
            <div class="register-form-field register-form-field--fare">
              <label for="fare">Price per hour</label>
              <pv-input-mask mask="S/ 99.99" v-model="formState.price" />
              <small>* includes commission</small>
            </div>
            <div class="register-form-field register-form-field--description">
              <label for="description">Description</label>
              <pv-input-text v-model="formState.description" />
            </div>
            <div class="post-container">
              <i v-if="loading" class="pi pi-spin pi-spinner" />
              <pv-button
                v-else
                @click="handlePostGarage()"
                class="register-form-post-btn"
                label="Post parking"
                severity="contrast"
              />
            </div>
          </div>
        </div>
      </form>
    </section>
  </v-base-layout>
</template>

<style scoped>
.main-section {
  display: flex;
  flex-direction: column;
  height: 100%;
}
.section-title {
  font-size: 48px;
  font-family: 'Rubik', sans-serif;
  color: #3c4e67;
  font-weight: 700;
  letter-spacing: -0.5px;
  margin-block: 0 12px;
}
.section-subtitle {
  font-size: 16px;
  margin-block: 0;
}
.section-header + .p-divider {
  margin-bottom: 24px;
}

.register-form-section--top {
  display: flex;
  gap: 1.5rem;
  margin-bottom: 1.25rem;
}
.register-form-field {
  display: flex;
  flex-direction: column;
}
.register-form-field small {
  margin-top: 4px;
}
.register-form-field--address {
  width: 100%;
}
.register-form-field--spaces {
  flex-shrink: 0;
  width: 15rem;
}
.register-form-additional-info .register-form-field :deep(.p-inputtext) {
  width: 100%;
}
.register-form-field label {
  margin-bottom: 0.5rem;
}
.map-container {
  width: 100%;
  aspect-ratio: 3/1;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.15);
}
.register-form-additional-info {
  display: flex;
  gap: 1rem;
  margin-top: 1.25rem;
}
.register-form-cta {
  display: flex;
  gap: 1.25rem;
  margin-top: 1.5rem;
}
.register-form-field--fare {
  width: 20rem;
}
.register-form-field--description {
  width: 100%;
}
.register-form-post-btn {
  text-wrap: nowrap;
  flex-shrink: 0;
  height: fit-content;
  margin-block: auto;
  background: #3c4e67;
}
.post-container {
  display: flex;
  align-items: center;
}

@media screen and (max-width: 1080px) {
  .map-container {
    min-height: 16rem;
  }
  .register-form-field--spaces {
    flex-shrink: initial;
  }
  .register-form-additional-info {
    flex-wrap: wrap;
  }
  .register-form-cta {
    display: flex;
    flex-wrap: wrap;
  }
  .section-title {
    font-size: 32px;
    margin-bottom: 8px;
  }
  .section-subtitle {
    font-size: 14px;
  }
  .register-form-section--top {
    display: flex;
    flex-direction: column;
  }
  .main-section label {
    font-size: 13px;
    margin-bottom: 2px;
  }
  .main-section :deep(.p-inputtext) {
    font-size: 12px;
  }
  .map-container {
    width: 100%;
    aspect-ratio: initial;
    min-height: 320px;
  }
}
</style>
