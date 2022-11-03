<template>
  <div class="content">
    <h1>Tic - Tac - Toe</h1>
    <h2>By: Brad Butterfield</h2>

    <h3 v-if="winner">{{ winner }} wins!</h3>

    <template v-if="gameSize === 0">
      <label for="game-size">Select Game Board Size:</label>
      <select name="game-size" id="game-size" v-model="gameSize">
        <option :value="0" disabled selected>Pick an option</option>
        <option v-for="size in 8" :value="size + 2" :key="size">
          {{ size + 2 }}
        </option>
      </select>
    </template>

    <template v-if="gameSize > 0 && !winner">
      <h3>It is {{ whosTurn }}'s turn</h3>

      <div class="row" v-for="row in gameSize" :key="row">
        <box-component
          v-for="col in gameSize"
          :key="col"
          :whosTurn="whosTurn"
          :row="row"
          :col="col"
          @box-component:log-move="logMove"
        />
      </div>
    </template>
  </div>
</template>

<script>
import BoxComponent from './components/BoxComponent.vue';

export default {
  name: 'App',
  components: {
    BoxComponent,
  },
  data() {
    return {
      gameSize: 0,
      whosTurn: 'X',
      winner: '',
      xMoves: [],
      oMoves: [],
    };
  },
  methods: {
    logMove(row, col) {
      if (this.whosTurn === 'X') {
        this.xMoves.push({
          row,
          col,
        });
      }

      if (this.whosTurn === 'O') {
        this.oMoves.push({
          row,
          col,
        });
      }

      this.switchTurns();
    },
    switchTurns() {
      if (this.whosTurn === 'X') {
        this.whosTurn = 'O';
      } else {
        this.whosTurn = 'X';
      }
    },
    checkMoves(moves, mover) {
      const rowObject = {};
      const colObject = {};

      moves.forEach(function (move) {
        //if a team has (gameSize) of the same row
        if (rowObject[move.row]) {
          rowObject[move.row] = rowObject[move.row] + 1;
        } else {
          rowObject[move.row] = 1;
        }

        //if a team has (gameSize) of the same col
        if (colObject[move.col]) {
          colObject[move.col] = colObject[move.col] + 1;
        } else {
          colObject[move.col] = 1;
        }
      });

      Object.values(rowObject).forEach((rowCount) => {
        if (rowCount === this.gameSize) {
          this.winner = mover;
        }
      });

      Object.values(colObject).forEach((colCount) => {
        if (colCount === this.gameSize) {
          this.winner = mover;
        }
      });
    },
    checkForwardDiagonalMoves(moves, mover) {
      let diagonalCount = 0;

      moves.forEach(function (move) {
        if (move.row === move.col) {
          diagonalCount = diagonalCount + 1;
        }
      });

      //if a team has (gameSize) of matching rows and cols
      if (diagonalCount === this.gameSize) {
        this.winner = mover;
      }
    },
    checkBackwardDiagonalMoves(moves, mover) {
      const self = this;
      let counter = 0;

      moves.forEach(function (move) {
        if (move.row + move.col === self.gameSize + 1) {
          counter = counter + 1;
        }
      });

      if (counter === this.gameSize) {
        this.winner = mover;
      }
    },
  },
  watch: {
    xMoves: {
      handler(moves) {
        this.checkMoves(moves, 'X');
        this.checkForwardDiagonalMoves(moves, 'X');
        this.checkBackwardDiagonalMoves(moves, 'X');
      },
      deep: true,
    },
    oMoves: {
      handler(moves) {
        this.checkMoves(moves, 'O');
        this.checkForwardDiagonalMoves(moves, 'O');
        this.checkBackwardDiagonalMoves(moves, 'O');
      },
      deep: true,
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

.content {
  display: flex;
  flex-flow: column nowrap;
  align-items: center;
  padding: 1rem;
}

.row {
  display: flex;
  border-bottom: 1px solid black;
}

.row:last-child {
  border: none;
}
</style>
