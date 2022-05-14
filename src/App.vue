<script setup>
import MonsterGame from './components/MonsterGame.vue';
</script>

<template>
<header>
  <div class="game-title">
    <h1 class="heading">
      Monster Slayer
    </h1>
  </div>
</header>
<main>
  <MonsterGame />
</main>
</template>

<style>
@import './assets/base.css';
.game-title{
  display:flex;
  flex-wrap: wrap;
  justify-content: center;
  background-color: rgb(179, 19, 19);
  padding: 10px;
}
.heading{
  font-weight: bold;
  color: whitesmoke;
}
main{
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  padding: 20px;
}
</style>
