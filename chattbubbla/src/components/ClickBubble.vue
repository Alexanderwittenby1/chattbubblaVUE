<template>
    <div class="chat-container">
      <!-- Flyttbar chattbubbla -->
      <div 
        class="circle" 
        @click="toggleChat"
        @mousedown="startDrag"
        :style="{ top: circlePosition.top + 'px', left: circlePosition.left + 'px' }"
      >
        💬
      </div>
  
      <!-- Chattfönster  -->
      <div 
        v-if="showChat" 
        class="chat-window" 
        :style="{ top: chatPosition.top + 'px', left: chatPosition.left + 'px' }"
      >
        <div class="chat-header">
          <span>ChatNPT</span>
          <button @click="toggleChat" class="chat-window-close">✖</button>
        </div>
  
        <div class="chat-messages">
          <div v-for="(msg, index) in messages" :key="index" class="message">
            {{ index }}  {{ msg }}
          </div>
        </div>
  
        <!-- Inputfält -->
        <input 
          v-model="newMessage"
          type="text"
          class="chat-input"
          placeholder="What can i help you with?"
          @keydown.enter="sendMessage"
        />
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "ClickCircle",
    data() {
      return {
        showChat: false,  // Styr om chattfönstret visas
        newMessage: "",   // Håller det aktuella meddelandet
        messages: [],     // Lista med skickade meddelanden
        isDragging: false, // Om användaren drar cirkeln
        circlePosition: { top: 0, left: 0 }, // Startposition för cirkeln
        offset: { x: 0, y: 0 } // Musens offset från klickpunkten
      };
    },
    computed: {
      // 🛠 Chattfönstrets position RELATIVT till cirkelns position
      chatPosition() {
        return {
          top: this.circlePosition.top -90,  // Justerar höjd 
          left: this.circlePosition.left - 300  // Justerar vänsterposition 
        };
      }
    },
    methods: {
      toggleChat() {
        this.showChat = !this.showChat; // Öppna/stäng chattfönstret
      },
      sendMessage() {
        if (this.newMessage.trim() !== "") {
          this.messages.push(this.newMessage); // Lägg till meddelandet i listan
          this.newMessage = ""; // Rensa inputfältet
        }
      },
      startDrag(event) {
        this.isDragging = true;
        this.offset.x = event.clientX - this.circlePosition.left;
        this.offset.y = event.clientY - this.circlePosition.top;
        
        document.addEventListener("mousemove", this.onDrag);
        document.addEventListener("mouseup", this.stopDrag);
      },
      onDrag(event) {
        if (this.isDragging) {
          this.circlePosition.left = event.clientX - this.offset.x;
          this.circlePosition.top = event.clientY - this.offset.y;
        }
      },
      stopDrag() {
        this.isDragging = false;
        document.removeEventListener("mousemove", this.onDrag);
        document.removeEventListener("mouseup", this.stopDrag);
      }
    }
  };
  </script>
  
  <style scoped>
  /* Flyttbar chattbubbla */
  .circle {
    width: 60px;
    height: 60px;
    background-color: rgb(0, 33, 95);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-size: 24px;
    cursor: grab; /* Indikation på att man kan dra den :) */
    transition: background-color 0.3s;
    position: absolute; /* Sätter position till absolute för att kunna flytta. */
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
    border: 2px solid gray;
  }
  
  .circle:hover {
    background-color: rgb(69, 133, 250);
    transition: background-color 0.3s ease-in-out, opacity 0.3s ease-in-out;
  }
  
  /* Chattfönster */
  .chat-window {
    position: absolute; /* Viktigt för att göra den flyttbar */
    width: 280px;
    background: white;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
    padding: 10px;
    border: 1px solid;
    cursor: default;
  }


  
  /* Chattfönstrets header */
  .chat-header {
    display: flex;
    justify-content: space-between;
    font-weight: bold;
    padding-bottom: 5px;
    border-bottom: 1px solid #ddd;
    color: rgb(0, 33, 95);
  }
  
  .chat-header button {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 16px;
  }
  
  /* Chattmeddelanden */
  .chat-messages {
    max-height: 150px;
    overflow-y: auto;
    margin-bottom: 10px;
    scrollbar-color: rgb(0, 33, 95) #fff;
  }
  
  .message {
    background: #f1f1f1;
    padding: 8px;
    border-radius: 5px;
    margin-bottom: 5px;
    color: rgb(0, 33, 95);
    text-align: left;
    word-wrap: break-word; /* Bryt långa ord till nästa rad */
    white-space: pre-wrap; 
  }
  
  /* Inputfält */
  .chat-input {
    width: 100%;
    padding-top: 4px;
    border: 1px solid #ddd;
    border-radius: 5px;
    outline: none;
  }
  
  .chat-window-close {
    color: #333;
  }
  </style>
  