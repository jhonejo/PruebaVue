<script>
export default {
  props: ['leadId'],
  data() {
    return {
      ruc: '',
      rucData: null,
      email: '',
      selectedSubsidiary: null,
      errorMessage: ''
    }
  },
  methods: {
    async fetchRUCData() {
      try {
        const response = await fetch(`https://test.wanqara.com/api/ruc/${this.ruc}`)
        const data = await response.json()
        if (data.status) {
          this.rucData = data.data
        } else {
          this.errorMessage = 'RUC no válido'
        }
      } catch (error) {
        this.errorMessage = 'Error en la conexión'
      }
    },
    async saveLead() {
      try {
        const response = await fetch('https://test.illarli.com/api/lead', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            id: this.leadId,
            ruc: this.ruc,
            businessname: this.rucData.businessname,
            address: this.selectedSubsidiary.address,
            commercialname: this.selectedSubsidiary.commercial_name,
            code: this.selectedSubsidiary.code,
            email: this.email
          })
        })
        const data = await response.json()
        if (data.status) {
          alert('Datos guardados exitosamente')
        } else {
          this.errorMessage = 'Error al guardar datos'
        }
      } catch (error) {
        this.errorMessage = 'Error en la conexión'
      }
    }
  }
}
</script>

<template>
  <div>
    <div v-if="!rucData" class="container">
      <h1>Datos del RUC</h1>

      <form @submit.prevent="fetchRUCData">
        <div class="containerInput">
          <label>RUC</label>
          <input v-model="ruc" type="text" required />
        </div>

        <div class="containerButton">
          <button type="submit">Consultar</button>
        </div>
      </form>
    </div>

    <div v-if="rucData" class="container">
      <div class="containerInput">
        <label>Razón Social</label>
        <input v-model="rucData.businessname" type="text" readonly />
      </div>

      <div class="containerInput">
        <label>Correo Electrónico</label>
        <input v-model="email" type="email" required />
      </div>

      <div class="containerInput">
        <label>Sucursal</label>
        <select
          v-model="selectedSubsidiary"
          style="
            height: 30px;
            width: 300px;
            border-radius: 5px;
            border: 1px solid black;
            margin-top: 5px;
          "
        >
          <option
            v-for="subsidiary in rucData.subsidiaries"
            :key="subsidiary.code"
            :value="subsidiary"
          >
            {{ subsidiary.commercial_name }}
          </option>
        </select>
      </div>

      <div class="containerButton">
        <button @click="saveLead">Guardar</button>
      </div>

      <div class="error" v-if="errorMessage">{{ errorMessage }}</div>
    </div>
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
