<template>
    <div class="chatbox-history">
        <h3>Historial</h3>
        <hr>
        <!-- <span class="msg-info">Usuario 1 dijo:</span> -->
        <!-- Cuando solo había un mensaje... -->
        <!-- <span :style="{ backgroundColor: color1 }" class="msg1 msg-txt">{{ mensage1 }}</span> -->

        <!-- Aqui ahora necesito mostrar toda la historia de mensajes, usando un ciclo que recorra todo el objeto 'history'. -->
        <!-- Aquí va el historial, en forma de un ciclo de lista -->
        <ul class="history">
            <li v-for="(message, index) in history" :key="index">
                <span :class="{'msg1 msg-txt': message.user1, 'msg2 msg-txt': !message.user1 }">
                    {{ message.message }}
                </span>

             <!--    <span :class="{'msg1': message.username === 'randomUser1', 'msg2': message.username === 'randomUser2', 'msg-txt': true}" >
                    {{ message.message }}
                </span> -->
               <!--  <span class="msg-info">{{ message.username }} dijo:</span> -->
            </li>
        </ul>
        

    </div>    
</template>

<script>
import { ref } from 'vue';

export default {
    name: 'Chatbox',
    // Aquí se le pasa el objeto 'history' que se obtuvo de la API.
    props: {
        username1: String,
        message1: String,
        username2: String,
        message2: String,
        color1: String,
        color2: String,
        history: Object,
        user1: Boolean
    },
    
    methods: {
       
        selectColor1() {
            return this.color1
        }
    }
}


</script>

<style scoped>
.chatbox-history {
    border: 1px solid white;
    height: auto;
    width: 450px;
    padding: 1rem;
    margin: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    border-radius: 10px;
    overflow-y: scroll;
    background-color: #31363F;
}

.msg-info {
    display: block;
    text-align: left;
    font-style: italic;
    margin-bottom: 0.5rem;
    
}

.msg1 {
    display: inline;
    align-self: flex-start;
    background-color: v-bind('color1');
    /* background-color: blue; */
    padding: .75rem;
    float: left;
    border-radius: 0px 16px 16px 16px;
    border: 1px solid white;
    width:fit-content;
    text-align: left;
    max-width: 100%;
    margin-bottom: 0.25rem;
    
}

.msg2 {
    display: inline;
    align-self: flex-end;
    background-color: v-bind('color2');
    padding: .75rem;
    float: right;
    border-radius: 16px 0px 16px 16px;
    border: 1px solid white;
    width:fit-content;
    text-align: right;
    max-width: 100%;
    margin-bottom: 0.25rem;
    
}

.msg-txt {
    display: block;
    width:min-content;
    text-wrap:pretty;
    word-wrap: break-word;
}

ul {
    list-style-type: none;
}

hr {
    border: 1px solid white;
    width: 100%;
}

.history    {
    display: flex;
    flex-direction: column;
    align-items: space-between;
    margin: 0;
    padding: 1rem;
    width: 100%;

}
</style>