<template>
    <div class="container mt-5">
        <div class="row my-2 mb-4">
            <div class="col-12 mb-4">
                <div class="card text-center">
                    <div class="card-header">
                        Ronda
                    </div>
                    <div class="card-body">
                        <h5 class="card-title">{{round}}</h5>
                    </div>
                </div>
            </div>
            <div class="col-6 text-center">
                <label for=""><h2><strong>NS</strong></h2></label><br>
                <input type="number" style="padding: 5px; font-size:3em;" class="text-center" placeholder="NS" :disabled="disabled" max="99" v-model="ns">

            </div>
            <div class="col-6 text-center">
                <label for=""><h2><strong>G</strong></h2></label><br>
                <input type="number" style="padding: 5px; font-size:3em;" class="text-center" placeholder="G" :disabled="disabled" max="99" v-model="g"><br>
                <button :class="{'active' : selected_start_g_0}" class="btn btn-success my-2" @click="selectStart(0)" :disabled="automatico">0</button>
                <button :class="{'active' : selected_start_g_1}" class="btn btn-success my-2" @click="selectStart(1)" :disabled="automatico">1</button>
            </div>
        </div>
        <div class="col-12 text-center">
            <button class="btn btn-success my-2" :disabled="automatico" @click="validar()">Manual</button>
            <button class="btn btn-success my-2" :class="{'active' : automatico}" :disabled="automatico" @click="generateRamdomAutomatic()">Automatico</button>
            <button class="btn btn-success my-2" @click="resetData()"  :disabled="automatico">Reset</button>
            <button class="btn btn-success my-2" @click="stop()">Stop</button>

        </div>
        <div class="row">
            <div v-for="number in numbers" v-if="loading" class="p-2">
                <span v-for="n in number.count" class="p-2 my-2">{{ number.number }} <span class="badge bg-success" v-if="number.new && number.count == n">new</span></span>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                ns : 0,
                g : 0,
                numbers : [],
                aux : false,
                round : 0,
                order : [],
                loading : true,
                timer : '',
                automatico : false,
                bandera : false,
                disabled : false,
                selected_start_g_1 : false,
                selected_start_g_0 : true,
                start_g : 0
            }
        },
        created(){
        },
        methods:{
            selectStart(num){
                if(num == 0){
                    this.selected_start_g_0 = true
                    this.selected_start_g_1 = false
                }else{
                    this.selected_start_g_0 = false
                    this.selected_start_g_1 = true
                }
                this.start_g = num
            },
            resetData(){
                this.ns = 0
                this.g = 0
                this.numbers = []
                this.aux = false
                this.round = 0
                this.order = []
                this.loading = true
                this.automatico = false
                this.bandera = false
                this.disabled = false
                this.selected_start_g_1 = false
                this.selected_start_g_0 = false
                this.start_g =  0
            },
            stop(){
                clearInterval(this.timer);
                this.automatico = false
            },
            randomNumber(min, max) {
                min = Math.ceil(min);
                max = Math.floor(max);
                return Math.floor(Math.random() * (max - min + 1)) + min;
            },
            generateRamdomAutomatic(){
                this.automatico = true
                this.timer = setInterval(()=>{
                    this.generateRamdom()
                } ,2000)
            },
            validar(){
                if(this.ns > this.g){
                    alert('Ns Debe ser mayor a G')
                }else{
                    this.generateRamdom()
                }
            },
            generateRamdom(){
                this.round++
                if(this.automatico){
                    this.bandera = false
                    while (!this.bandera) {
                        this.ns = this.randomNumber(0,99)
                        this.g = this.randomNumber(0,99)
                        if(this.ns < this.g){
                            this.start_g = this.randomNumber(0,1)
                            this.bandera = true
                        }
                    }
                }
                if(this.round > 1){
                    this.numbers.forEach(number => {
                        number.new = false
                    });
                }
                for (let index = 0; index < this.ns; index++) {
                    this.aux = false
                    let number = this.randomNumber(this.start_g, this.g)
                    for (let j = 0; j < this.numbers.length; j++) {
                        if(number == this.numbers[j].number){
                            if(this.round == 1){
                                index--
                                this.aux = true
                                break
                            }else{
                                if(this.numbers[j].count < this.round && !this.numbers[j].new){
                                    this.numbers[j].count++
                                    this.numbers[j].new = true
                                }else{
                                    index --
                                }
                                this.aux = true
                                break
                            }

                        }else{
                            this.aux = false
                        }
                    }
                    if(!this.aux){
                        this.numbers.push({
                            'number' : number,
                            'count' : 1,
                            'new' : true
                        })
                    }
                }
                this.numbers = _.orderBy(this.numbers, ['number'],
                ['adc']);
            }
        }
    }
</script>
