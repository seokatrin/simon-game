<template>
  <div class="game-options">
    <p class="game-level">Раунд: {{ score }}</p>
    <button
      class="game-start-btn"
      :disabled="isOn"
      @click="this.$emit('start-game')"
    >
      Старт
    </button>
    <p class="game-hard">Сложность игры:</p>
    <label
      class="game-radio-btn"
      v-for="(level, index) in gameLevels"
      :key="level"
    >
      {{ level }}
      <input
        type="radio"
        :disabled="isOn"
        v-model="modelValue"
        :value="index"
        @change="updateInput"
      />
    </label>
  </div>
</template>
<script>
export default {
  name: "game-options",
  props: {
    isOn: { type: Boolean },
    gameLevels: { type: Array },
    modelValue: { type: Number },
    score: { type: Number },
  },
  methods: {
    updateInput(e) {
      this.$emit("update:modelValue", e.target.value);
    },
  },
};
</script>