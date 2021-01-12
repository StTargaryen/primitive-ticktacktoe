<template>
  <div class="container">
    <div class="game">
      <h2 v-if="winner">Winner: {{ winner }} 🥳</h2>
      <h2 v-else>Player Move: {{ player }} 🤔</h2>
      <button @click="reset" class="btn btn-success mb-3">Reset</button>

      <div v-for="(_, x) in 3" :key="x" class="row">
        <button @click="move(x, y)" v-for="(_, y) in 3" :key="y" class="square">
          {{ squares[x][y] }}
        </button>
      </div>

      <h2 class="mt-5">History</h2>
      <button @click="resetHistory" class="btn btn-success mb-3">
        Reset history
      </button>
      <div v-for="(game, index) in history" :key="index">
        Game {{ index + 1 > 9 ? index + 1 : `0${index + 1}` }} | {{ game }} won
      </div>
    </div>
  </div>
</template>

<script>
import { computed, onMounted, ref, watch } from "vue";

const calculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];

    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
    return null;
  }
};

export default {
  setup() {
    const player = ref("X");
    const squares = ref([
      ["", "", ""],
      ["", "", ""],
      ["", "", ""],
    ]);
    const winner = computed(() => calculateWinner(squares.value.flat()));

    const move = (x, y) => {
      if (winner.value) return;
      squares.value[x][y] = player.value;
      player.value = player.value === "0" ? "X" : "0";
    };

    const reset = () => {
      player.value = "X";
      squares.value = [
        ["", "", ""],
        ["", "", ""],
        ["", "", ""],
      ];
    };

    const history = ref([]);
    watch(winner, (current, previous) => {
      if (current && !previous) {
        history.value.push(current);
        localStorage.setItem("history", JSON.stringify(history.value));
      }
    });

    onMounted(() => {
      history.value = JSON.parse(localStorage.getItem("history")) ?? [];
    });

    const resetHistory = () => {
      history.value = [];
      localStorage.setItem("history", JSON.stringify(history.value));
    };

    return { player, squares, winner, move, reset, history, resetHistory };
  },
};
</script>

<style scoped>
.container {
  position: relative;
}

.container > .game {
  position: absolute;
  top: calc(50% + 100px);
  left: calc(50% - 100px);
  width: 100vw;
  height: 100vh;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 60px;
  font-weight: bold;
  height: 100px;
  border-radius: 6px;
  width: 100px;
  margin: 4px;
}
</style>