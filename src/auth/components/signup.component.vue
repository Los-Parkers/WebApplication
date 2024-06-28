<script>
import { AuthService } from '@/auth/services/auth.service.js'

export default {
  name: 'sign-up-component',
  data() {
    return {
      country: '',
      fullname: '',
      email: '',
      password: '',
      confirmPassword: '',
      phoneNumber: '',
      newsCheck: '',
      privacyCheck: '',
      authService: new AuthService()
    }
  },

  created() {},

  methods: {
    register(event) {
      event.preventDefault()
      this.authService
        .signUp(
          this.fullname,
          this.password,
          this.email,
          this.country,
          this.phoneNumber,
          this.newsCheck,
          this.privacyCheck
        )
        .then(() => {
          this.$toast.add({
            severity: 'success',
            summary: 'Success',
            detail: 'Redirecting to login...',
            life: 2000
          })
          setTimeout(() => {
            this.$router.push('/login')
          }, 2000)
        })
        .catch(() => {
          this.$toast.add({
            severity: 'error',
            summary: 'Error',
            detail: 'Error while registering user. Please try again.',
            life: 1000
          })
        })
    }
  }
}
</script>

<script setup>
import PvInputText from 'primevue/inputtext'
import PvPassword from 'primevue/password'
import PvCascadeSelect from 'primevue/cascadeselect'
import PvInputNumber from 'primevue/inputnumber'
import PvCheckbox from 'primevue/checkbox'
import PvButton from 'primevue/button'
</script>

<template>
  <section class="form-container">
    <form class="form" v-on:submit="register($event)">
      <div class="form-item">
        <div class="logo-container">
          <img alt="Logo" class="logo" src="../../assets/logo.svg" />
        </div>
        <h1 class="form-title">Sign Up</h1>
      </div>
      <div class="form-item">
        <label class="form-label" for="fullname">Full Name</label>
        <pv-input-text
          id="fullname"
          class="form-input"
          v-model="fullname"
          aria-describedby="fullname-help"
        />
      </div>
      <div class="form-item">
        <label class="form-label" for="email">E-mail</label>
        <pv-input-text
          id="email"
          class="form-input"
          v-model="email"
          aria-describedby="email-help"
        />
      </div>
      <div class="duo-container">
        <div class="form-item">
          <label class="form-label" for="password">Password</label>
          <pv-password class="form-input" v-model="password" :feedback="false" toggleMask />
        </div>
        <div class="form-item">
          <label class="form-label" for="password">Confirm Password</label>
          <pv-password class="form-input" v-model="confirmPassword" :feedback="false" toggleMask />
        </div>
      </div>
      <div class="duo-container">
        <div class="form-item">
          <label class="form-label" for="country">Country</label>
          <pv-cascade-select id="country" class="form-select" v-model="country" />
        </div>
        <div class="form-item">
          <label class="form-label" for="phone">Phone Number</label>
          <pv-input-number
            class="form-input"
            v-model="phoneNumber"
            :useGrouping="false"
            inputId="withoutgrouping"
          />
        </div>
      </div>
      <div class="form-item">
        <div class="conditions-container">
          <div class="condition-item">
            <pv-checkbox class="checkbox" v-model="newsCheck" inputId="condition1" :binary="true" />
            <label for="condition1">I want to receive news from EzPark via email</label>
          </div>
          <div class="condition-item">
            <pv-checkbox
              class="checkbox"
              v-model="privacyCheck"
              inputId="condition1"
              :binary="true"
            />
            <label for="condition2">I have read and agree with the Privacy Policy</label>
          </div>
        </div>
      </div>
      <div class="form-item">
        <pv-button id="submit" type="submit" class="form-btn" label="Sign Up" />
      </div>
      <p class="login-link">
        Already have an account? <router-link to="/login">Sign in</router-link>
      </p>
    </form>
  </section>
</template>

<style scoped>
a {
  text-decoration: none;
  color: #ef6c42;
}

.form {
  display: flex;
  align-items: center;
  background-color: white;
  flex-direction: column;
  font-family: 'Mulish', 'Inter var', sans-serif;

  padding: 20px;
  width: 500px;
  border-radius: 20px;
}

.form-title {
  font-family: 'Rubik', sans-serif;
  color: #212529;
  margin-block: 0.75rem 0;
  font-size: 1.75rem;

  font-weight: 700;
  line-height: 1.2;
}
.logo {
  width: 160px;
}

.form-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: inherit;

  margin-bottom: 20px;
  color: black;
  width: 80%;
}

.duo-container {
  display: flex;
  gap: 2em;
  flex-direction: row;
  font-family: inherit;

  width: 80%;
}

.form-label {
  width: 100%;
  font-weight: 700;
  margin-bottom: 0.25rem;
}

.form-input {
  width: 100%;
}

.form-input :deep(.p-inputtext),
.form-input.p-inputtext,
.form-select {
  width: 100%;
  background-color: #f7f7f7;
  color: #000;
}
.form-input :deep(.p-inputtext):focus,
.form-input.p-inputtext:focus {
  outline: 2px solid #ef6c42;
}
.form-select {
  outline-color: #ef6c42;
  outline-width: 2px;
}

.conditions-container {
  display: inherit;
  flex-wrap: wrap;

  width: 368px;
}

.condition-item {
  display: inherit;
  gap: 1em;
  font-size: smaller;

  margin-top: 5px;
}

.checkbox {
  color: red;
}

.form-btn {
  font-family: 'Rubik', sans-serif;
  color: #fff;
  background: #000;
  padding-block: 0.625rem;
  width: 100%;
  border: none;
  outline: none;
  box-shadow: none;
}

.login-link a:hover {
  text-decoration: underline;
}
</style>