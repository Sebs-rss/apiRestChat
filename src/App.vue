<!-- Esta página muestra un chat de dos personas.
App.vue es el componente padre, en dónde se renderiza el componente hijo.
App.vue se conectará a la API de Random User (https://randomuser.me/) para obtener datos de dos personas a usar luego en el componente hijo Chatbox, con algunas de las propiedades obtenidas (props). -->
<!-- App.vue contiene los métodos async para que traigan los datos (Data) -->
<template>
  <div id="app">
    <h1>Chat de dos personas 💬</h1>
    <div class="chat-container">
      <!-- Aquí va la persona aleatoria # 1 -->
      <div class="user-container">
        <h4>#1: {{ randomUser1.name.first }}</h4>
        <div v-if="randomUser1">
          <p style="font-style: italic;">{{ randomUser1.name.first }} {{ randomUser1.name.last }}</p>
          <img :src="randomUser1.img" alt="Persona 1" class="user-img" :style="{ 'outline': `6px solid ${color1}` }">
          <hr>
          <form>
            <!-- Aquí se elije el color de los mensajes del usuario 1 -->
            <div class="color-selector">
              <label for="color1">Tu color ➡️:</label>
              <input type="color" id="color1" v-model= "color1">
            </div>
            <!-- Aquí se ingresa el mensaje que será parte del historial en el Chatbox -->
            <div class="msg-box">
              <label for="message">Escribe un mensaje:</label>
              <textarea id="message" v-model="message1" rows="3"></textarea>
              <button type="button" class="btn btn-primary" @click.prevent="sendMessage1">Enviar</button>
            </div>
          </form>
        </div>
        <p v-else>{{ error }}</p>
      </div>
      <!-- Aquí va insertado el componente hijo Chatbox, que irá recogiendo y guardando los mensajes de ambas personas  -->
      <Chatbox :randomUser1="randomUser1" :randomUser2="randomUser2" :color1="color1" :color2="color2" :history="history"/>
      <!-- <ChatBox :randomUser1="randomUser1" :randomUser2="randomUser2" :message1="message1" :message2="message2" /> -->
      <!-- Aquí va la segunda persona. Como mejora se podría componentizar al usuario para solo reutilizar -->
      
      <!-- Aquí va la persona aleatoria # 2 -->
      <div class="user-container">
        <h4>#2: {{ randomUser2.name.first }}</h4>
        <div v-if="randomUser2">
          <p style="font-style: italic;">{{ randomUser2.name.first }} {{ randomUser2.name.last }}</p>
          <img :src="randomUser2.img" alt="Persona 2" class="user-img" :style="{ 'outline': `6px solid ${color2}` }">
          <hr>
          <form>
            <!-- Aquí se elije el color de los mensajes del usuario 2 -->
            <div class="color-selector">
              <label for="color2">Tu color ➡️:</label>
              <input type="color" id="color2" v-model= "color2">
            </div>
            <!-- Aquí se ingresa el mensaje que será parte del historial en el Chatbox -->
            <div class="msg-box">
              <label for="message">Escribe un mensaje:</label>
              <textarea id="message" v-model="message2" rows="3"></textarea>
              <button type="button" class="btn btn-primary" @click.prevent="sendMessage2">Enviar</button>
            </div>
          </form>
        </div>
        <p v-else>{{ error }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { ref } from 'vue'; // Para que vaya tomando los cambios en History reactivamente
import Chatbox from './components/Chatbox.vue'

    export default {
        components: {
            Chatbox
        },
      
        props: {
            username1: String,
            message1: String,
            color1: String,
            color2: String,
            history: Array,
            user1: Boolean
        },
        data() {
            return {
                randomUser1: { name: { first: '', last: '' }, img: '' },
                randomUser2: { name: { first: '', last: '' }, img: '' },
                user1: '',
                error: 'No data available',
                message1: '...',
                message2: '',
                color1: '#430A5D',
                color2: '#40A2E3',
                //history: {}, // Un objeto que guardará los mensajes de ambas personas dentro de un historial, que será mostrado en el Chatbox 
                history: ref([]), // Cambiamos history a un array reactivo
              }
        },
        mounted() {
          console.log("Mounted: calling randomUser method")
          this.randomUser()
          console.log("Mounted: randomUser method finished")
        },
        

        methods: {
            async randomUser() {
            try {
                console.log("Fetching random user...")
                const url = `https://randomuser.me/api/?results=2`
                const response = await axios.get(url)
                if (response && response.data && response.data.results) {
                    const data = response.data
                    console.log("Data fetched successfully:", data)
                    if (data.results.length >= 2) {
                        this.randomUser1 = { name: data.results[0].name, img: data.results[0].picture.large }
                        this.randomUser2 = { name: data.results[1].name, img: data.results[1].picture.large }
                        console.log("Random user data set:", this.randomUser1)
                        console.log("Random user data set:", this.randomUser2)
                    } else {
                        throw new Error("Insufficient data received")
                    }
                } else {
                    throw new Error("Invalid data structure")
                }
            } catch (error) {
                console.error("Error fetching random user:", error)
                this.randomUser1 = { name: { first: '', last: '' }, img: '' }
                this.randomUser2 = { name: { first: '', last: '' }, img: '' }
                this.error = "Personas no encontradas"
            }
            },
            sendMessage1() {
            console.log("Sending message1:", this.message1);
            
            /* ALt A */
            this.history = { ...this.history, [Date.now()]: { username: this.randomUser1.name.first, message: this.message1 , user1: true} };
            this.history = Object.entries(this.history).map(([key, value]) => ({ key, ...value }));
            this.message1 = '';
            console.log("Message1 sent and history updated:", this.history);
            },

            sendMessage2() {
            console.log("Sending message2:", this.message2);
            this.history = { ...this.history, [Date.now()]: { username: this.randomUser2.name.first, message: this.message2, user1: false } };
            this.history = Object.entries(this.history).map(([key, value]) => ({ key, ...value }));
            this.message2 = '';
            console.log("Message2 sent and history updated:", this.history);
          },
        }
    }
</script>

<style scoped>
  #app {
    height: auto;
    border: 1px solid white;
    color: aliceblue;
    text-align: center;
    padding: 2rem;
    border-radius: 8px;
    margin-top: 2rem;
  }

  .user-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 20px;
    border: 1px solid white;
    height: auto;
    border-radius: 10px;
    padding: 1rem;
    margin: 0;
  }

  .chat-container {
    display: flex;
    flex-direction: row;
    align-items:stretch;
    justify-content: center;
    gap: 1rem;
    margin-top: 2rem;
  }

  form {
    display: flex;
    flex-direction: column;
    align-items: start;
  }

/* Para poner el color de fondo del nombre de la persona personalizado */
  /* 
.user-title {
  width: 100%;
} */

  .msg-box {
    display: flex;
    flex-direction: column;
    align-items: start;
    gap: 6px;
  }

  .color-selector {
    display:inline-flex;
    align-items: center;
    width: 100%;
    justify-content: space-between;
  }

  .user-img {
    border-radius: 50%;
    border: 2px solid #242424;
  }

  .btn {
    width: 100%;
  }

</style>
