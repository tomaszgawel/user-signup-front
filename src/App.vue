<template>
  <div class="container">
    <h1>User Signup</h1>
    <div class="row justify-content-md-center">
      <div class="form-group">
        <div class="col mb-3">
          <input type="email" class="form-control" placeholder="Username" v-model="usernameInput"
                 @blur="validateUsername">
        </div>
        <div class="col mb-3">
          <span v-if="errors.usernameMessage" class="error-message">{{ errors.usernameMessage }}</span>
        </div>
        <div class="col mb-3">
          <input type="password" class="form-control" placeholder="Password" v-model="passwordInput "
                 @blur="validatePassword">
        </div>
        <div class="col mb-3">
          <input type="password" class="form-control" placeholder="Password again" v-model="passwordAgainInput"
                 @blur="validatePassword">
        </div>
        <div class="col mb-3">
          <span v-if="errors.passwordMessage" class="error-message">{{ errors.passwordMessage }}</span>
        </div>
        <div class="col">
          <button class="button-28" @click="handleClick">Sign up</button>
        </div>
        <modal :show="popupActive" :message="this.responseMessage" @close="popupActive = false">
          <template #body>
            {{this.responseMessage}}
          </template>
        </modal>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Modal from './components/ResponseModal.vue'

export default {
  name: 'App',
  components: {
    Modal
  },
  methods: {
    clearForm: function () {
      this.usernameInput = '';
      this.passwordInput = '';
      this.passwordAgainInput = '';
    },
    handleClick: async function () {
      if (this.validatePassword() && this.validateUsername()) {
        let payload = {username: this.usernameInput, password: this.passwordInput};
        try {
          await axios.post("http://localhost:8081/api/v1/user", payload);
          this.clearForm();
          this.responseMessage = "User signed up"
          this.popupActive = true;
        } catch (e) {
          this.responseMessage = e.response.data.message;
          this.popupActive = true;
        }
      }
    },
    validateUsername: function () {
      if (this.usernameInput) {
        if (this.usernameInput.length < 5 || !this.usernameInput.match(/^[0-9a-zA-Z]+$/)) {
          this.errors.usernameMessage = "Username needs to have more then 5 alphanumeric characters"
          return false;
        } else {
          this.errors.usernameMessage = ''
          return true;
        }
      }
      return false;
    },
    validatePassword: function () {
      if (this.passwordInput || this.passwordAgainInput) {
        const pattern = new RegExp("^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d).+$");
        if (this.passwordInput !== this.passwordAgainInput) {
          this.errors.passwordMessage = "Passwords need to match"
          return false
        } else if (this.passwordInput.length < 8 || !pattern.test(this.passwordInput)) {
          this.errors.passwordMessage = "Password needs to have more then 8 characters, 1 uppercase letter, 1 lowercase letter and 1 number"
          return false
        } else {
          this.errors.passwordMessage = ""
          return true
        }
      }
      return false;
    }
  },
  data() {
    return {
      usernameInput: '',
      passwordInput: '',
      passwordAgainInput: '',
      responseMessage: '',
      popupActive: false,
      errors: {
        usernameMessage: '',
        passwordMessage: ''
      },
    }
  },
  watch: {
    usernameInput: function () {
      this.validateUsername();
    },
    passwordInput: function () {
      this.validatePassword();
    },
    passwordAgainInput: function () {
      this.validatePassword()
    }
  }
}
</script>

<style>
.container {
  max-width: 50%;
}

.error-message {
  font-size: 10px;
  color: #fc3131;
}

.button-28 {
  appearance: none;
  background-color: transparent;
  border: 2px solid #1A1A1A;
  border-radius: 15px;
  color: #3B3B3B;
  cursor: pointer;
  font-family: Roobert, -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  font-size: 16px;
  font-weight: 600;
  line-height: normal;
  margin: 0;
  min-height: 60px;
  min-width: 0;
  outline: none;
  padding: 16px 24px;
  text-align: center;
  text-decoration: none;
  transition: all 300ms cubic-bezier(.23, 1, 0.32, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  will-change: transform;
}

.button-28:disabled {
  pointer-events: none;
}

.button-28:hover {
  color: #fff;
  background-color: #1A1A1A;
  box-shadow: rgba(0, 0, 0, 0.25) 0 8px 15px;
  transform: translateY(-2px);
}

.button-28:active {
  box-shadow: none;
  transform: translateY(0);
}
</style>
