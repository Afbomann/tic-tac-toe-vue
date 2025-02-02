<script setup lang="ts">
import { onMounted, ref } from "vue";

type TGameText = "Your turn!" | "Computer is thinking..." | "You win!" | "You lose!" | "Draw!";

const gameStarted = ref<boolean>(false);
const gameBoard = ref<string[]>(["", "", "", "", "", "", "", "", ""]);
const gameText = ref<TGameText>("Your turn!");
const wins = ref<number>(0);

const makeTurn = async (index: number) => {
  if (gameText.value != "Your turn!" || gameBoard.value[index] != "") return;

  gameBoard.value[index] = "X";

  const gameOver = checkWinner();

  //@ts-expect-error works
  if (gameText.value == "You win!") {
    wins.value++;
    localStorage.setItem("tic-tac-toe-wins", wins.value.toString());
  }

  if (!gameOver) await computerTurn();
};

const checkWinner = (): boolean => {
  if (gameBoard.value[0] == "X" && gameBoard.value[1] == "X" && gameBoard.value[2] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[3] == "X" && gameBoard.value[4] == "X" && gameBoard.value[5] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[6] == "X" && gameBoard.value[7] == "X" && gameBoard.value[8] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[0] == "X" && gameBoard.value[3] == "X" && gameBoard.value[6] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[1] == "X" && gameBoard.value[4] == "X" && gameBoard.value[7] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[2] == "X" && gameBoard.value[5] == "X" && gameBoard.value[8] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[0] == "X" && gameBoard.value[4] == "X" && gameBoard.value[8] == "X") {
    gameText.value = "You win!";
    return true;
  } else if (gameBoard.value[2] == "X" && gameBoard.value[4] == "X" && gameBoard.value[6] == "X") {
    gameText.value = "You win!";
    return true;
  }

  if (gameBoard.value[0] == "O" && gameBoard.value[1] == "O" && gameBoard.value[2] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[3] == "O" && gameBoard.value[4] == "O" && gameBoard.value[5] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[6] == "O" && gameBoard.value[7] == "O" && gameBoard.value[8] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[0] == "O" && gameBoard.value[3] == "O" && gameBoard.value[6] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[1] == "O" && gameBoard.value[4] == "O" && gameBoard.value[7] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[2] == "O" && gameBoard.value[5] == "O" && gameBoard.value[8] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[0] == "O" && gameBoard.value[4] == "O" && gameBoard.value[8] == "O") {
    gameText.value = "You lose!";
    return true;
  } else if (gameBoard.value[2] == "O" && gameBoard.value[4] == "O" && gameBoard.value[6] == "O") {
    gameText.value = "You lose!";
    return true;
  }

  if (gameBoard.value.every((value) => value != "")) {
    gameText.value = "Draw!";
    return true;
  }

  return false;
};

const computerTurn = async () => {
  gameText.value = "Computer is thinking...";

  const randomTime = Math.round(Math.random() + 1) * 1000;
  await new Promise((resolve) => setTimeout(() => resolve(true), randomTime));

  let randomNumber;

  do {
    randomNumber = Math.floor(Math.random() * 9);
  } while (gameBoard.value[randomNumber] != "");

  gameBoard.value[randomNumber] = "O";

  const gameOver = checkWinner();

  //@ts-expect-error works
  if (gameText.value == "You win!") {
    wins.value++;
    localStorage.setItem("tic-tac-toe-wins", wins.value.toString());
  }

  if (!gameOver) gameText.value = "Your turn!";
};

onMounted(() => {
  const winsData = localStorage.getItem("tic-tac-toe-wins");

  if (!winsData || !parseInt(winsData)) return;

  wins.value = parseInt(winsData);
});
</script>

<template>
  <div class="w-[600px] max-w-[85%] mx-auto my-[5dvh] flex flex-col items-center">
    <h2 class="text-xl lg:text-2xl font-bold">Tic-Tac-Toe</h2>
    <p class="text-base lg:text-lg mt-[1dvh]">Wins: {{ wins }}</p>

    <button
      class="cursor-pointer bg-green-400 px-[15px] py-[4px] rounded-xl mt-[2dvh] text-sm lg:text-base"
      v-if="!gameStarted"
      @click="() => (gameStarted = true)"
    >
      Start Game
    </button>

    <div class="flex mt-[2dvh] flex-wrap w-[300px]" v-if="gameStarted">
      <div
        :class="[
          index == 0 && `rounded-tl-xl`,
          index == 2 && `rounded-tr-xl`,
          index == 6 && `rounded-bl-xl`,
          index == 8 && `rounded-br-xl`,
        ]"
        class="w-1/3 h-[50px] outline-1 outline-gray-300 text-center content-center text-[25px] font-bold cursor-pointer"
        v-for="(value, index) in gameBoard"
        :key="index"
        @click="async () => await makeTurn(index)"
      >
        {{ value }}
      </div>
    </div>

    <p class="text-base lg:text-lg mt-[2dvh]" v-if="gameStarted">{{ gameText }}</p>

    <div class="flex gap-[2dvh] mt-[2dvh]">
      <button
        class="cursor-pointer bg-green-400 px-[15px] py-[4px] rounded-xl text-sm lg:text-base"
        v-if="
          gameStarted && (gameText == `You win!` || gameText == `You lose!` || gameText == `Draw!`)
        "
        @click="
          () => {
            gameBoard = gameBoard.map(() => ``);
            gameText = `Your turn!`;
          }
        "
      >
        New Game
      </button>
      <button
        class="cursor-pointer bg-red-400 px-[15px] py-[4px] rounded-xl text-sm lg:text-base"
        v-if="
          gameStarted && (gameText == `You win!` || gameText == `You lose!` || gameText == `Draw!`)
        "
        @click="
          () => {
            gameStarted = false;
            gameBoard = gameBoard.map(() => ``);
            gameText = `Your turn!`;
          }
        "
      >
        Exit
      </button>
    </div>
  </div>
</template>
