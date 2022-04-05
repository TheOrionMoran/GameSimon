<template>
<div class="main">
    <h1>Игра Simon</h1>
    <div class="difficulty">
        <h2>Уровень сложности: 
            <span v-if="difficulty == 0">Легкий</span>
            <span v-if="difficulty == 1">Средний</span>
            <span v-if="difficulty == 2">Сложный</span>
        </h2>
        <div class="difficultyButtonWrapper">
            <button v-on:click="difficultyFunc(0)">Легкий</button>
            <button v-on:click="difficultyFunc(1)">Средний</button>
            <button v-on:click="difficultyFunc(2)">Сложный</button>
        </div>
    </div>
    <div class="gameField">
        <div class="buttonWrapper" id="buttonWrapper">
            <button class="button1" id="1" v-on:click="click(1)">.</button>
            <button class="button2" id="2" v-on:click="click(2)">.</button>
            <br>
            <button class="button3" id="3" v-on:click="click(3)">.</button>
            <button class="button4" id="4" v-on:click="click(4)">.</button>
        </div>
        <div class="starter">
            <button v-on:click="startGame()">Новая игра</button>
            <p>Раунд: {{raund}}</p>
        </div>
    </div>

</div>
</template>

<script>
export default {
    name: 'mainComponent',
    data() {
        return{
            difficulty: 0,
            difftime: 0,
            steps: new Array,
            clicks: new Array,
            timeInput: false,
            raund: 0
        }
    },
    methods: {
        getRandomInt: function (min, max){
            min = Math.ceil(min);
            max = Math.floor(max);
            return Math.floor(Math.random() * (max - min + 1)) + min;
        },
        sleep: function(milliseconds){
             return new Promise(resolve => setTimeout(resolve, milliseconds));
        },
        generator: function(a){
            for (let i = 0; i < a; i++) {
                let number = this.getRandomInt(1,4);
                this.steps.push({number})
                console.log('l: ' + number);
            }
        },
        async startGame (){

            this.raund = 1;
            await this.sleep(1000);
            this.nextStap();
            switch (this.difficulty) {
                case 0:
                    this.difftime = 1500;
                break;

                case 1:
                     this.difftime = 1000;
                break;

                case 2:
                     this.difftime = 400;
                break;

            }

        },
        click: function (id){
            if (this.timeInput == true) {
                let number = id;
                this.clicks.push({number});

                let audio = new Audio(require('../assets/'+ id +'.ogg'));
                audio.play();



                if(this.clicks.length == 2 + this.raund){
                    const arraysAreEqual = JSON.stringify(this.clicks)==JSON.stringify(this.steps);
                    if(arraysAreEqual){
                        this.raund++;
                        console.log('Верно');
                        this.accept();
                        this.nextStap();
                    }else{
                        alert("Вы проиграли");
                        this.clicks = 0;
                        this.raund = 0;
                    }
                }
            }

        },
        async nextStap() {
            let a = 2 + this.raund;
            this.timeInput = false;

            this.clicks = [];
            this.steps = [];

            this.generator(a);

            for (let i = 0; i < this.steps.length; i++) {
                await this.sleep(this.difftime); 
                console.log(i);
                document.getElementById(this.steps[i].number).click();
                document.getElementById(this.steps[i].number).classList.add('click');


                let audio = new Audio(require('../assets/'+ this.steps[i].number +'.ogg'));
                audio.play();

                await this.sleep(this.difftime); 
                document.getElementById(this.steps[i].number).classList.remove('click');
            }
            this.timeInput = true;
        },
        difficultyFunc: function (dif){
            this.difficulty = dif;
            this.raund = 0;
        },
        async accept(){
            document.getElementById("buttonWrapper").classList.add('accept');
            await this.sleep(1000); 
            document.getElementById("buttonWrapper").classList.remove('accept');
        }
    }

}
</script>