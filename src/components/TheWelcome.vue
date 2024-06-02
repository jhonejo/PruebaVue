<script>
export default {
  data() {
    return {
      nombre: '',
      apellido: '',
      telefono: '',
      otp: '',
      otpSent: false,
      errorMessage: '',
      errorMessage1: ''
    }
  },
  methods: {
    async sendOTP() {
      try {
        const response = await fetch(
          `https://test.wanqara.com/api/send-code?phone=${this.telefono}`
        )

        const data = await response.json()

        if (data.status === true) {
          this.otpSent = true
        } else {
          this.errorMessage = 'Error al enviar OTP'
          data()
        }
      } catch (error) {
        this.errorMessage = 'Error en la conexión'
      }
    },
    async verifyOTP() {
      try {
        const response = await fetch(`https://test.wanqara.com/api/verify-code?code=${this.otp}`, {
          method: 'POST'
        })
        const data = await response.json()

        if (data.status === true) {
          const leadResponse = await fetch(`https://test.wanqara.com/api/lead`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
              name: this.nombre,
              lastname: this.apellido,
              phone: this.telefono
            })
          })
          const leadData = await leadResponse.json()
          if (leadData.status) {
            this.$emit('leadCreated', leadData.data.id)
          } else {
            this.errorMessage1 = 'Error al guardar lead'
          }
        } else {
          this.errorMessage1 = 'OTP no válido'
        }
      } catch (error) {
        this.errorMessage1 = 'Error en la conexión'
      }
    }
  }
}
</script>

<template>
  <div>
    <div v-if="!otpSent" class="container">
      <h1>Datos Personales</h1>
      <form @submit.prevent="sendOTP">
        <div class="containerInput">
          <label>Nombre</label>
          <input v-model="nombre" type="text" class="form-input" required autocomplete="off" />
        </div>

        <div class="containerInput">
          <label>Apellido</label>
          <input v-model="apellido" type="text" class="form-input" required autocomplete="off" />
        </div>

        <div class="containerInput">
          <label>Teléfono</label>
          <input v-model="telefono" type="text" class="form-input" required autocomplete="off" />
        </div>

        <div class="containerButton">
          <button type="submit">
            <div class="svg-wrapper-1">
              <div class="svg-wrapper">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24">
                  <path fill="none" d="M0 0h24v24H0z"></path>
                  <path
                    fill="currentColor"
                    d="M1.946 9.315c-.522-.174-.527-.455.01-.634l19.087-6.362c.529-.176.832.12.684.638l-5.454 19.086c-.15.529-.455.547-.679.045L12 14l6-8-8 6-8.054-2.685z"
                  ></path>
                </svg>
              </div>
            </div>
            <span>Enviar</span>
          </button>
        </div>
      </form>
      <div class="error" v-if="errorMessage">{{ errorMessage }}</div>
    </div>

    <div v-if="otpSent" class="container">
      <div class="containerInput">
        <label>Ingrese el OTP</label>
        <input v-model="otp" type="text" class="form-input" required autocomplete="off" />
      </div>

      <div class="containerButton">
        <button @click="verifyOTP">Verificar OTP</button>
      </div>
    </div>
    <div class="error" v-if="errorMessage1">{{ errorMessage1 }}</div>
  </div>
</template>

<style>
h1 {
  color: black;
}

label {
  color: black;
}

input {
  height: 30px;
  width: 300px;
  border-radius: 5px;
  border: 1px solid black;
  margin-top: 5px;
}

.container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  border: 1px solid black;
  padding: 20px;
  border-radius: 5px;
  background: white;
}

.containerInput {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

.containerButton {
  display: flex;
  justify-content: center;
  align-items: center;
}

.error {
  color: red;
}

/* aqui */
button {
  font-family: inherit;
  font-size: 20px;
  background: royalblue;
  color: white;
  padding: 0.7em 1em;
  padding-left: 0.9em;
  display: flex;
  align-items: center;
  border: none;
  border-radius: 16px;
  overflow: hidden;
  transition: all 0.2s;
  cursor: pointer;
}

button span {
  display: block;
  margin-left: 0.3em;
  transition: all 0.3s ease-in-out;
}

button svg {
  display: block;
  transform-origin: center center;
  transition: transform 0.3s ease-in-out;
}

button:hover .svg-wrapper {
  animation: fly-1 0.6s ease-in-out infinite alternate;
}

button:hover svg {
  transform: translateX(1.2em) rotate(45deg) scale(1.1);
}

button:hover span {
  transform: translateX(5em);
}

button:active {
  transform: scale(0.95);
}

@keyframes fly-1 {
  from {
    transform: translateY(0.2em);
  }

  to {
    transform: translateY(-0.1em);
  }
}
</style>
