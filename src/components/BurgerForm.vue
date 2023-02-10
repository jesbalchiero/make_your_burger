<template>
    <div id="main-form">
        <Message :message="message" v-show="message" />
        <div> 
            <form id="burger-form" @submit="submitBurger">
                <div class="input-container">
                    <label for="name">Client Name: </label>
                    <input type="text" id="name" name="name" v-model="clientName" placeholder="Tell us your name...">
                </div>
                <div class="input-container">
                    <label for="bread">Select a Bread: </label>
                    <select name="bread" v-model="breadSelected" id="bread">
                        <option value="">Select the desired bread...</option>
                        <option v-for="bread in breads" :key="bread.id" :value="bread.tipo">{{bread.tipo}}</option>
                    </select>
                </div>
                <div class="input-container">
                    <label for="meat">Select a Meat: </label>
                    <select name="meat" v-model="meatSelected" id="meat">
                        <option value="">Select the desired meat...</option>
                        <option v-for="meat in meats" :key="meat.id" :value="meat.tipo">{{meat.tipo}}</option>
                    </select>
                </div>
                <div id="additionals-container" class="input-container">
                    <label id="additionals-title" for="additionals">Choose more options: </label>
                    <div class="checkbox-container" v-for="additional in additionals" :key="additional.id">
                        <input type="checkbox" name="additionals" v-model="additionalsSelected" :value="additional.tipo" />
                        <span>{{ additional.tipo }}</span>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Take the Order!">
                </div>
            </form>
        </div>
    </div>
</template>

<script>
    import Message from './Message.vue';

    export default{
        name: "BurgerForm",
        data(){
            return {
                breads: null,
                meats: null,
                additionals: null,
                clientName: null,
                breadSelected: null,
                meatSelected: null,
                additionalsSelected: [],
                status: "Requested",
                message: null,
            }
        },
        methods:{
            async getIngredients(){
                const req = await fetch('http://localhost:3000/ingredientes');
                const data = await req.json();

                this.breads = data.paes;
                this.meats = data.carnes;
                this.additionals = data.opcionais;
            },
            async submitBurger(e){
                e.preventDefault();
                
                const req = await fetch('http://localhost:3000/burgers', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        name: this.clientName,
                        bread: this.breadSelected,
                        meat: this.meatSelected,
                        additionals: Array.from(this.additionalsSelected),
                        status: "Solicited"
                    })
                });

                const res = await req.json();

                this.callMessage();
                this.cleanSubmit();
            },
            cleanSubmit(){
                this.clientName = null;
                this.breadSelected = null;
                this.meatSelected = null;
                this.additionalsSelected = [];
            },
            callMessage(){
                this.message = `Order ${res.id} added with sucessfully!`

                setTimeout(() => {
                    this.message = null;
                }, 3000);
            }
        },
        mounted(){
            this.getIngredients();
        },
        components:{
            Message
        }
    }
</script>

<style scoped>
    #burger-form {
        max-width: 400px;
        margin: 0 auto;
    }

    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #FCBA03;
    }

    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #additionals-container {
        flex-direction: row;
        flex-wrap: wrap;
    }

    #additionals-title {
        width: 100%;
    }

    .checkbox-container {
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }

    .checkbox-container span, 
    .checkbox-container input {
        width: auto;
    }

    .checkbox-container span {
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn {

        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }

    .submit-btn:hover{
        background-color: #FCBA03;
        color: #222;
    }
</style>