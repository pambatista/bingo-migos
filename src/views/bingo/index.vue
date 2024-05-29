<template>
  <div class="bingo__container">
    <div class="numero">
      <div class="numero__display">
        <span>Número sorteado:</span>
        <h1 v-if="numeroSorteado" class="text-8xl">
          {{ numeroSorteado }}
        </h1>
        <h1 v-else class="text-8xl">Iniciar</h1>
        <button
          class="btn btn-sorteio"
          @click="sortearNumero"
          :disabled="loading"
        >
          Sortear Número
        </button>
      </div>
      <div>
        <button class="btn btn-limpar" @click="limparBingo">Limpar jogo</button>
      </div>
    </div>
    <div class="bingo">
      <div v-for="(letra, index) in letras" :key="letra">
        <div class="bingo__titulo">
          <strong>
            {{ letra }}
          </strong>
        </div>

        <div
          v-for="numero in gerarNumeros(index)"
          :key="numero"
          class="bingo__numeros"
          :class="{
            '!bg-purple-500 text-white': numerosChamados.includes(numero),
          }"
        >
          <strong>
            {{ numero }}
          </strong>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const letras = ["B", "I", "N", "G", "O"];
const numeroFrases = {
  1: "começou o jogo",
  5: "Aperte o cinto",
  9: "Pingo no pé é",
  12: "Vitamina",
  13: "Faz u L",
  22: "Dois patinhos na lagoa",
  24: "Vinhado",
  30: "O carro",
  33: "Idade de Cristo",
  51: "Uma boa ideia",
  66: "Um tapa atrás da orelha",
  75: "Acabou o jogo",
};
const frasesMigos = [
  "isso é, é,  tu, tudo pessoal",
  "Dos males o pior!",
  "James Franco!",
  "Existe vida melhor mas não presta!",
  "Existe vida melhor mas é caríssima!",
];

const numeroSorteado = ref(null);
const loading = ref(false);
const numerosChamados = ref([]);

function limparBingo() {
  numerosChamados.value = [];
  numeroSorteado.value = null;
}
function gerarNumeros(index) {
  const inicio = index * 15 + 1;
  const fim = inicio + 14;
  const numeros = [];
  for (let i = inicio; i <= fim; i++) {
    numeros.push(i);
  }
  return numeros;
}
async function sortearNumero() {
  loading.value = true;

  try {
    const numero = gerarNumeroAleatorio(1, 75);
    if (!numerosChamados.value.includes(numero)) {
      numeroSorteado.value = numero;
      numerosChamados.value.push(numero);
      if (numero <= 15) {
        await falarNumero(
          ` ${numeroFrases[numero] ? numeroFrases[numero] : ""} B ${numero}`
        );
      } else if (numero > 15 && numero <= 30) {
        await falarNumero(
          ` ${numeroFrases[numero] ? numeroFrases[numero] : ""} I ${numero}`
        );
      } else if (numero > 30 && numero <= 45) {
        await falarNumero(
          ` ${numeroFrases[numero] ? numeroFrases[numero] : ""} N ${numero}`
        );
      } else if (numero > 45 && numero <= 60) {
        await falarNumero(
          ` ${numeroFrases[numero] ? numeroFrases[numero] : ""} Ge ${numero}`
        );
      } else if (numero > 60 && numero <= 75) {
        await falarNumero(
          ` ${numeroFrases[numero] ? numeroFrases[numero] : ""} ó ${numero}`
        );
      }
    } else {
      if (numerosChamados.value.length < 75) {
        sortearNumero();
      } else {
        const fraseSorteada =
          frasesMigos[Math.floor(Math.random() * frasesMigos.length)];
        await falarNumero(fraseSorteada);
      }
    }
  } catch (error) {
    console.log(error);
  } finally {
    setTimeout(() => {
      loading.value = false;
    }, 1000);
  }
}
function gerarNumeroAleatorio(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
function falarNumero(frase) {
  const utterance = new SpeechSynthesisUtterance(frase.toString());
  speechSynthesis.speak(utterance);
}
</script>

<style>
.bingo__container {
  @apply p-8 md:grid grid-cols-2 gap-4;
  .bingo {
    @apply grid grid-cols-5 gap-4;
    .bingo__titulo {
      @apply block bg-blue-500 p-4 text-white text-center rounded-lg mb-4;
    }
    .bingo__numeros {
      @apply text-center p-2 rounded-md mb-2 bg-gray-100;
    }
  }
  .numero {
    @apply flex flex-col justify-center items-center;
    .numero__display {
      @apply flex-1 flex flex-col justify-center items-center w-full;
    }
  }

  .btn {
    @apply text-white p-4 rounded-lg mb-8;
  }
  .btn-limpar {
    @apply bg-gray-500;
  }
  .btn-sorteio {
    @apply bg-green-500 w-[200px];
  }
}
</style>
