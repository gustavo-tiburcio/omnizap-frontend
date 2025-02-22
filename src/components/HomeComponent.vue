<template>
  <div class="Home">
    <div class="error" v-show="error">
      Escreva um nome e uma mensagem para enviar uma mensagem!!
    </div>

    <div class="title" style="color: rgb(25, 255, 94);">Omnizap</div>

    <div class="subTitle">Seu Nome:</div>
    <input class="inputMsg" type="text" v-model="nomeUsuario" placeholder="Digite seu nome">

    <div class="subTitle">Escreva sua mensagem aqui ↓</div>
    <input class="inputMsg" type="text" v-model="mensagem" @keyup.enter="enviarMensagem" placeholder="Digite sua Mensagem">

    <div>
      <div class="title">Mensagens</div>
        <div class="msg" v-show="mensagens.length > 0">
          <div v-for="(msg, index) in mensagens" :key="index" v-html="formatarMensagem(msg)"></div>
        </div>
        <div>
          <div class="subTitle">Maximo de mensagens</div>
          <input type="number" style="width: 40px;" class="inputMsg" v-model="maxMensagens">
        </div>
    </div>
  </div>
</template>

<script>
import { io } from 'socket.io-client';
export default {
  data() {
    return {
      error: false,
      nomeUsuario: "",
      maxMensagens: 7,
      mensagem: "",  // Usando mensagem para armazenar a mensagem digitada
      mensagens: [],  // Lista para armazenar as mensagens recebidas
      coresUsuarios: {}    
    };
  },
  created() {
    // Conectar ao servidor e ouvir as mensagens recebidas
    this.socket = io('https://742e-201-43-246-2.ngrok-free.app');
    this.socket.on('receberMensagem', (msg) => {
      console.log('📩 Mensagem recebida no frontend:', msg);
      if (this.mensagens.length >= this.maxMensagens) {
        this.mensagens = [];
      }
      this.mensagens.push(msg);
    });
  },
  methods: {
    enviarMensagem() {
      if (this.mensagem.trim() !== "" && this.nomeUsuario.trim() !== "") {
        this.error = false;
        const msgFormatada = `${this.nomeUsuario}: ${this.mensagem}`;
        this.socket.emit('enviarMensagem', msgFormatada);
        this.mensagem = "";
      } else {
        this.error = true;
      }
    },
    formatarMensagem(msg) {
      const [nome, ...texto] = msg.split(": ");
      const cor = this.definirCorUsuario(nome);
      return `<span style="color: ${cor}; font-weight: bold;">${nome}</span>: ${texto.join(": ")}`;
    },
    definirCorUsuario(nome) {
      if (!this.coresUsuarios[nome]) {
        const cores = ["#e74c3c", "#3498db", "#2ecc71", "#f39c12", "#9b59b6", "#1abc9c", "#d35400", "#c0392b"];
        let hash = 0;
        for (let i = 0; i < nome.length; i++) {
          hash = nome.charCodeAt(i) + ((hash << 5) - hash);
        }
        const index = Math.abs(hash) % cores.length;
        this.coresUsuarios[nome] = cores[index];
      }
      return this.coresUsuarios[nome];
    }
  }
};
</script>

<style scoped>
.inputMsg {
  margin-top: 8px;
  outline: none;
  border-radius: 17px;
  border: none;
  padding: 10px;
  padding-inline: 12px;
  width: 300px;
  background-color: rgb(241, 241, 241);
  color: rgb(0, 0, 0);
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.575);
  font-size: 16px;
}

.subTitle {
  margin-top: 16px;
}

.inputMsg:hover {
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.808);
}

.title {
  display: inline-block;
  margin-block: 16px;
  font-size: 36px;
}

.msg {
  width: 90vw;
  max-width: 600px;
  border-radius: 17px;
  background-color: rgb(241, 241, 241);
  padding: 10px;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.575);
  text-align: start;
}

.error {
  padding: 10px;
  padding-inline: 12px;
  max-width: 600px;
  min-width: 500px;
  border-radius: 8px;
  margin-top: 8px;
  font-size: 16px;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.575);
  background-color: rgb(226, 60, 60);
  color: white;
}

</style>
