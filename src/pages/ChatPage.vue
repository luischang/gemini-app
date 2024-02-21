<template>
  <div class="q-pa-md row justify-center">
    <div style="width: 100%; max-width: 600px">
      <q-chat-message v-for="(message, index) in messages" :key="index" class="q-mb-sm" :right="index % 2 === 0"
        :sent="message.sentByUser">
        <span v-html="formatMessage(message)"></span>
      </q-chat-message>
      <q-chat-message v-if="loading" name="Gemini">
        <q-spinner-dots size="2rem" />
      </q-chat-message>
      <q-input v-model="prompt" @keyup.enter="send" filled rounded dense placeholder="Escribe tu mensaje..." />
    </div>
  </div>
  <q-page>

  </q-page>
</template>

<script>
import { ref } from 'vue';
import { GoogleGenerativeAI } from "@google/generative-ai";

export default {
  name: 'ChatPage',
  data() {
    return {
      messages: [],
      prompt: '',
      genAI: null,
      model: null,
      loading: false
    };
  },
  created() {
    const API_KEY = "AIzaSyBlFC7KUyLITsyNVoNhJmKtXlXaQgDqEmQ";
    this.genAI = new GoogleGenerativeAI(API_KEY);
    this.model = this.genAI.getGenerativeModel({ model: "gemini-pro" });
  },
  methods: {
    async send() {
      await this.sendPrompt();
    },
    async sendPrompt() {
      if (!this.prompt.trim()) return;
      this.loading = true
      let newMessage = this.prompt
      this.prompt = '';
      this.messages.push({ text: newMessage, sentByUser: true });

      const result = await this.model.generateContent(newMessage);
      const response = await result.response;

      const text = await response.text();
      this.messages.push({ text: text, sentByUser: false });
      this.loading = false
    },
    formatMessage(message) {
      let formattedText = message.text;
      console.log(formattedText)
      // Expresión regular para detectar títulos de libros entre dobles asteriscos
      const bookTitleRegex = /\*\*(.*?)\*\*/g;

      // Reemplazar los títulos de libros detectados por el texto con formato HTML
      formattedText = formattedText.replace(bookTitleRegex, '<b>$1</b>');

      // Expresión regular para detectar elementos de lista con asteriscos
      const listItemRegex = /\* (.*?)\n/g;

      // Reemplazar los elementos de lista con formato HTML
      formattedText = formattedText.replace(listItemRegex, '<li>$1</li>');

      // Envolver el contenido de la lista con etiquetas <ul>
      formattedText = formattedText.replace('<li>', '<ul><li>');

      // Si el mensaje es una lista, agregar las etiquetas de cierre </ul>
      if (formattedText.includes('<ul>')) {
        formattedText += '</ul>';
      }

      return formattedText;
    }

  }
};
</script>

<style scoped>
/* Estilos opcionales para el chat */
.q-chat-message {
  max-width: 70%;
}
</style>
