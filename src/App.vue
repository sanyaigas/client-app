<template>
  <div id="app">
    <div class="chat">
      <div class="messages">
        <div v-for="message in messages" :key="message.id">{{ message.text }}</div>
      </div>
      <input v-model="newMessage" @keyup.enter="sendMessage" placeholder="Type a message" />
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  data() {
    return {
      newMessage: '',
      messages: [],
      socket: null,
    };
  },
  created() {
    this.socket = io('http://localhost:3000');
    this.socket.on('message', this.receiveMessage);
    this.socket.on('connect', () => console.log('Connected to server'));
    this.socket.on('disconnect', () => console.log('Disconnected from server'));
  },
  methods: {
    sendMessage() {
      if (this.newMessage.trim() !== '') {
        this.socket.emit('message', this.newMessage);
        this.newMessage = '';
      }
    },
    receiveMessage(message) {
      this.messages.push({ text: message, id: this.messages.length });
    },
  },
};
</script>

<style>
#app {
  text-align: center;
  margin-top: 60px;
}
.chat {
  width: 300px;
  margin: auto;
}
.messages {
  border: 1px solid #ccc;
  padding: 10px;
  max-height: 200px;
  overflow-y: auto;
  margin-bottom: 10px;
}
</style>
