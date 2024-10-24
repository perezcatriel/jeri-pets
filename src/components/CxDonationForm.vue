<template>
  <div class="donation-form">
    <h2>Elige tu tipo de donación</h2>

    <div class="donation-options">
      <button 
        v-for="type in donationTypes" 
        :key="type.value" 
        :class="{ active: donationType === type.value }" 
        @click="setDonationType(type.value)">
        {{ type.label }}
      </button>
    </div>

    <h3>Selecciona un monto:</h3>
    <div class="amount-options" v-if="donationType">
      <div class="prices">
        <button 
          v-for="price in prices[donationType]" 
          :key="price.value" 
          :class="{ active: amount === price.value }" 
          @click="setAmount(price.value)">
          R$ {{ price.value }}
        </button>
      </div>
    </div>

    <p class="description">{{ description }}</p>

    <label for="amount">O ingresa un monto personalizado:</label>
    <input type="number" v-model="amount" min="1" placeholder="R$50" @input="updateDescription" />

    <button @click="processPayment" class="donate-button">Donar {{ formattedAmount }}</button>

    <!-- Modal para mostrar el código QR -->
    <modal v-if="showModal" @close="showModal = false">
      <img :src="qrCode" alt="Código QR para Pix" />
      <p>Escanea el código QR para realizar tu donación.</p>
    </modal>
  </div>
</template>

<script>
export default {
  data() {
    return {
      donationType: 'monthly',
      amount: null,
      donationTypes: [
        { label: 'Donación Mensual', value: 'monthly' },
        { label: 'Donación Única', value: 'single' },
      ],
      prices: {
        monthly: [
          { value: 50, description: 'Ayuda con R$50 a alimentar a un perro durante una semana.' },
          { value: 100, description: 'Con R$100 podemos cubrir el tratamiento de un perro rescatado.' },
          { value: 200, description: 'R$200 nos permiten rescatar y cuidar a un animal durante un mes.' },
        ],
        single: [
          { value: 30, description: 'R$30 ayuda a comprar medicamentos esenciales.' },
          { value: 60, description: 'R$60 cubren la esterilización de un perro.' },
          { value: 120, description: 'Con R$120 podemos construir un refugio temporal.' },
        ],
      },
      showModal: false,
      qrCode: '',
    };
  },
  computed: {
    formattedAmount() {
      return this.amount ? `R$${this.amount}` : '';
    },
    description() {
      const selectedPrice = this.prices[this.donationType]?.find(price => price.value === this.amount);
      return selectedPrice ? selectedPrice.description : `Tu donación de R$${this.amount} ayudará a continuar con nuestra misión.`;
    },
  },
  methods: {
    setDonationType(type) {
      this.donationType = type;
      this.setAmount(this.prices[type][0].value); // Selecciona el primer valor por defecto
    },
    setAmount(value) {
      this.amount = value;
    },
    updateDescription() {
      this.description = this.amount ? `Tu donación de R$${this.amount} ayudará a continuar con nuestra misión.` : '';
    },
    async processPayment() {
      if (!this.amount) {
        alert('Por favor, ingresa un monto válido.');
        return;
      }

      // Datos de la transferencia
      const transferData = {
        amount: this.amount,
        cpf: '123.456.789-09', // Ejemplo de CPF, reemplázalo por el real o pídelo al usuario
        celular: '99999-9999', // Ejemplo de celular
      };

      // Lógica para generar el QR (reemplaza con tu endpoint real)
      try {
        const response = await fetch('/api/generate-pix', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(transferData),
        });
        const data = await response.json();
        this.showQRCode(data.qrCode); // Muestra el QR en un modal

        // También podrías mostrar los datos de transferencia
        alert(`Datos de transferencia:\nCPF: ${transferData.cpf}\nCelular: ${transferData.celular}\nMonto: R$${this.amount}`);
      } catch (error) {
        console.error('Error al procesar el pago:', error);
        alert('Hubo un problema al procesar la donación.');
      }
    },
    showQRCode(qrCode) {
      this.qrCode = qrCode;
      this.showModal = true;
    }
  },
  mounted() {
    // Selecciona el primer monto de la donación mensual por defecto
    this.setAmount(this.prices[this.donationType][0].value);
  },
};
</script>

<style scoped>
.donation-form {
  background-color: rgba(var(--color-black-rgb), 0.5);
  color: var(--color-white);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 0px 10px rgba(var(--color-black-rgb), 0.1);
  margin-top: 20px;
  z-index: 100;
  max-width: 500px;
}

h2 {
  background-color: var(--color-lightblue);
  padding: 10px;
}

.donation-options button,
.amount-options button {
  margin: 10px 0;
  padding: 10px 20px;
  border: none;
  background-color: var(--color-lightblue);
  color: var(--color-white);
  font-size: 18px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.donation-options button.active,
.amount-options button.active {
  background-color: var(--color-lightblue);
  font-weight: bold;
}

.donation-options button:not(.active),
.amount-options button:not(.active) {
  background-color: var(--color-black);
  color: var(--color-white);
}

.prices {
  margin-top: 10px;
}

.description {
  margin-top: 10px;
  background-color: rgba(var(--color-black-rgb), 0.5);
  padding: 20px 10px;
  border-radius: 5px;
  margin-bottom: 30px;
}

label {
  color: var(--color-lightblue);
  background-color: var(--color-black);
  font-weight: bold;
  padding: 10px;
  border-radius: 5px;
}

input {
  margin-top: 10px;
  padding: 10px;
  width: 100%;
  border: 1px solid var(--color-grey);
  border-radius: 5px;
}

.donate-button {
  margin-top: 10px;
  padding: 10px 20px;
  width: 100%;
  border-radius: 5px;
  background-color: var(--color-lightblue);
  border: none;
  color: var(--color-white);
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.donate-button:hover {
  background-color: rgba(var(--color-black-rgb), 0.8);
}
</style>
