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
            {{ user }}  {{ msg }}
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

        <button 
        @click="clearchat"
        class="clear-chat">Clear chat</button>
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
        circlePosition: { top: window.innerHeight - 80, left: window.innerWidth - 80 }, // Startposition för cirkeln
        offset: { x: 0, y: 0 }, // Musens offset från klickpunkten
        user: "User: "
      };
    },
    computed: {
      // 🛠 Chattfönstrets position RELATIVT till cirkelns position
      chatPosition() {
        let leftposition = this.circlePosition.left - 300; // Justerar vänsterposition
        
        if(this.circlePosition.left < 300) {
          leftposition = this.circlePosition.left + 60; // Justerar vänsterposition
        }
        if(this.circlePosition.top < 100){
          return {
            top: this.circlePosition.top +55,  // Justerar höjd 
            left: leftposition  // Justerar vänsterposition 
          };
        }
        return {
          top: this.circlePosition.top -90,  // Justerar höjd 
          left: leftposition  // Justerar vänsterposition 
        };
      }
    },
    methods: {
      toggleChat() {
        this.showChat = !this.showChat; // Öppna/stäng chattfönstret
      },
      sendMessage() {
        if (this.newMessage.trim() !== "") {
          if (this.newMessage.trim() === "dancerat") {
            this.messages.push("🐀🕺🏼");
          } else {
            this.messages.push(this.newMessage); // Lägg till meddelandet i listan
          }
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
          this.adjustPosition();
        }
      },
      stopDrag() {
        this.isDragging = false;
        document.removeEventListener("mousemove", this.onDrag);
        document.removeEventListener("mouseup", this.stopDrag);
      },
      clearchat() {
        this.messages = [];
      },
      adjustPosition() {
        if (this.circlePosition.top > window.innerHeight - 80) {
          this.circlePosition.top = window.innerHeight - 80;
        }
        if (this.circlePosition.left > window.innerWidth - 80) {
          this.circlePosition.left = window.innerWidth - 80;
        }
        if (this.circlePosition.top < 0) {
          this.circlePosition.top = 0;
        }
        if (this.circlePosition.left < 0) {
          this.circlePosition.left = 0;
        }
      },
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
    user-select: none;
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
    background: linear-gradient(270deg, rgba(0, 33, 95, 1) 0%, rgba(69, 133, 250, 1) 100%);
    overflow: hidden;
  }

  .clear-chat {
    background-color: red;
    color: white;
    border: none;
    padding: 5px;
    border-radius: 5px;
    cursor: pointer;
    margin-top: 10px;
    display: flex;
    user-select: none;
  }


  
  /* Chattfönstrets header */
  .chat-header {
    display: flex;
    justify-content: space-between;
    font-weight: bold;
    padding-bottom: 5px;
    border-bottom: 1px solid #ddd;
    color: #fff;
    user-select: none;
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
    margin-bottom: 5px;
    margin-top: 5px;
    scrollbar-color: rgb(0, 33, 95) #fff;
    overflow-y: scroll;
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
    width: 98%;
    /* padding: 4px; */
    border: 1px solid #ddd;
    
    outline: none;
  }
  
  .chat-window-close {
    color: #fff;
  }
  </style>
  