<template>
  <div class="game">
    <div class="game-button">
      <my-button
        v-for="(color, index) in colorsList"
        :key="color"
        :flash="color.flash"
        :color="color.color"
        :index="index"
        @on-btn-cliked="onColorBtnClicked"
      ></my-button>
    </div>
    <game-options
      v-model="selectedLevel"
      :score="score"
      :isOn="isOn"
      :gameLevels="gameLevels"
      @start-game="startGame"
    >
    </game-options>
  </div>
  <p class="game-message">{{ message }}</p>
</template>

<script>
import MyButton from "./components/MyButton.vue";
import GameOptions from "./components/GameOptions.vue";

import random from "./extraFunctions/random";
import timeout from "./extraFunctions/timeout";

export default {
  name: "App",
  components: { MyButton, GameOptions },
  data() {
    return {
      gameLevels: ["легкая", "средняя", "сложная"],
      selectedLevel: 0,
      timesList: [1500, 1000, 400],
      isComputerStep: false,
      score: 1,
      isOn: false,
      colorsList: [
        { color: "blue", flash: false },
        { color: "red", flash: false },
        { color: "yellow", flash: false },
        { color: "green", flash: false },
      ],
      computerColors: [],
      userColors: [],
      urlList: [
        "https://s3.amazonaws.com/freecodecamp/simonSound1.mp3",
        "https://s3.amazonaws.com/freecodecamp/simonSound2.mp3",
        "https://s3.amazonaws.com/freecodecamp/simonSound3.mp3",
        "https://s3.amazonaws.com/freecodecamp/simonSound4.mp3",
      ],
      message: "",
    };
  },
  methods: {
    startGame() {
      this.isOn = true;
      this.message = "";
    },
    
    playSound(sound) {
      if (sound) {
        const audio = new Audio(sound);
        audio.play();
      }
    },

    async onColorBtnClicked(btnIndex) {              // ф-ия выполняется по нажатию игрока на кнопку      
      if (!this.isComputerStep && this.isOn) {
        const iterableIndx =
          this.computerColors.length - this.userColors.length; //индекс текущей кнопки

        if (btnIndex === this.computerColors[iterableIndx]) {      //если нажатая кнопка соответсвует индексу в массиве computerColors
          this.flashColor(btnIndex); // подствечиваем соответсвующую кнопку и воспроизводим звук
          this.userColors.pop(); // удаляем из массива нажатую кнопку

          if (this.userColors.length === 0) {            // если нажаты все нужные кнопки передаем ход компьютеру
            this.isComputerStep = true; //  передаем ход компьютеру
            this.score++; //  увеличиваем раунд
          }
          await timeout(100);
          this.colorsList[btnIndex].flash = false; //  через 100 ms снимаем подстветку с кнопки
        } else {
          this.message = `Вы проиграли. И дошли до ${this.score} раунда`; //  если нажатая кнопка не соответсвует текущему индексу в массиве computerColors
          this.isOn = false; // игра окончена
        }
      }
    },

    async highlightBtns() {               // ф-ия выполняющая действия по нажатию на кнопки
      for (let i = 0; i < this.computerColors.length; i++) {        // по количеству кнопок в массиве
        const colorIndex = this.computerColors[i];
        await timeout(this.activeLevelTime); // перерыв между кнопками зависит от выбранногоо уровня

        this.flashColor(colorIndex); //  подствечиваем соответсвующую кнопку и воспроизводим звук

        await timeout(200);
        this.colorsList[colorIndex].flash = false; //  убираем подсветку через 200 ms
      }

      this.isComputerStep = false; //  когда все кнопки нажаты передаем ход игроку
      this.userColors = this.userColors.concat(this.computerColors).reverse(); //  создаем массив кнопока, которые должны быть нажаты игроком
    },

    flashColor(colorIndex) {
      this.colorsList[colorIndex].flash = true; //подствечиваем  кнопку
      const url = this.urlList[colorIndex]; // выбираем соответсвуюший url кнопки
      this.playSound(url);
    },

    resetGame() {
      this.isComputerStep = false;
      this.isUserStep = false;
      this.score = 1;
      this.computerColors = [];
      this.userColors = [];
    }
  },
  computed: {
    activeLevelTime() {
      return this.timesList[this.selectedLevel]; // устанавливаем время по выбранному уровню
    },
  },
  watch: {
    isOn(newValue) {
      if (newValue) {
        this.isComputerStep = true; // если нажата кнопка старт ходит компьютер
      } else {
        this.resetGame(); // если игрок нажал неправидьную кнопку, игра останавливается и обновляем состояние игры
      }
    },

    isComputerStep(newValue) {
      if (this.isOn && newValue) {         // если ход компьютера
        let newColor = random(0, this.colorsList.length - 1);
        this.computerColors.push(newColor); // добавляем новую кнопку
        this.highlightBtns(); // компьютер подствечивает кнопки
      }
    },
  },
};
</script>

<style lang="scss">
@import "./assets/styles";
</style>
