<template>
    <div id="burger-table" v-if="burgers">
        <Message :message="message" v-show="message" />
      <div>
        <div id="burger-table-heading">
          <div class="order-id">#:</div>
          <div>Client:</div>
          <div>Bread:</div>
          <div>Meat:</div>
          <div>Additionals:</div>
          <div>Actions:</div>
        </div>
      </div>
      <div id="burger-table-rows">
        <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
          <div class="order-number">{{ burger.id }}</div>
          <div>{{ burger.nome }}</div>
          <div>{{ burger.pao }}</div>
          <div>{{ burger.carne }}</div>
          <div>
            <ul v-for="(additional, index) in burger.opcionais" :key="index">
              <li>{{ additional }}</li>
            </ul>
          </div>
          <div>
            <select name="status" class="status" @change="updateBurger($event, burger.id)">
              <option :value="s.tipo" v-for="s in status" :key="s.id" :selected="burger.status == s.tipo">
                {{ s.tipo }}
              </option>
            </select>
            <button class="delete-btn" @click="cancelBurger(burger.id)">Cancel</button>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <h2>No orders at now!</h2>
    </div>
  </template>
  <script>
    import Message from './Message.vue';

    export default {
      name: "Dashboard",
      data() {
        return {
          burgers: null,
          status: [],
          message: null
        }
      },
      components: {
        Message
      },
      methods: {
        async getOrders() {
          const req = await fetch('http://localhost:3000/burgers')
  
          this.burgers = await req.json();
          this.getStatus();
        },
        async getStatus() {
          const req = await fetch('http://localhost:3000/status')

          this.status = await req.json();
        },
        async cancelBurger(id) {
          const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            method: "DELETE"
          });
  
          const res = await req.json();

          this.message = `Order ${res.id} canceled with sucessfully!`

          this.cleanMessage();
          this.getOrders();
        },
        async updateBurger(event, id) {  
          const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            method: "PATCH",
            headers: { 
                "Content-Type" : "application/json"
            },
            body: JSON.stringify({
                status: event.target.value
            })
          });
  
          const res = await req.json();

          this.message = `Order ${res.id} has changed to the status ${res.status}!`

          this.cleanMessage();
        },
        cleanMessage(){
            setTimeout(() => {
                this.message = null;
            }, 3000);
        }
      },
      mounted () {
        this.getOrders();
      }
    }
  </script>
  
  <style scoped>
    #burger-table {
      max-width: 1200px;
      margin: 0 auto;
    }
  
    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
      display: flex;
      flex-wrap: wrap;
    }
  
    #burger-table-heading {
      font-weight: bold;
      padding: 12px;
      border-bottom: 3px solid #333;
    }
  
    .burger-table-row {
      width: 100%;
      padding: 12px;
      border-bottom: 1px solid #CCC;
    }
  
    #burger-table-heading div,
    .burger-table-row div {
      width: 19%;
    }
  
    #burger-table-heading .order-id,
    .burger-table-row .order-number {
      width: 5%;
    }
  
    select {
      padding: 12px 6px;
      margin-right: 12px;
    }
  
    .delete-btn {
      background-color: #222;
      color:#fcba03;
      font-weight: bold;
      border: 2px solid #222;
      padding: 10px;
      font-size: 16px;
      margin: 0 auto;
      cursor: pointer;
      transition: .5s;
    }
    
    .delete-btn:hover {
      background-color: #fcba03;
      color: #222;
    }
    
  </style>