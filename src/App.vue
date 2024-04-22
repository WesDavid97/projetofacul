<script setup>
import {ref} from 'vue'
import { RouterView } from 'vue-router';

let num = ref(0)

</script>

<template>
  <!-- navbar -->
  <nav class="navbar navbar-expand-lg bg-primary text-light">
  <div class="container-fluid">
    <router-link class="navbar-brand" to="/"> 
      <img src="https://images.tcdn.com.br/img/img_prod/906697/squirtle_bro_361_1_4bed790bd1b747451226541a5d904bf1.png" alt="Bootstrap" width="40" height="50">     
      PokeVue
    </router-link>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
          <router-link class="nav-link" aria-current="page" to="/">Home</router-link>
        </li>        
      </ul>
    </div>
  </div>
</nav>

<!-- content -->
<div class="main">
  <router-view></router-view>
</div>

<footer class="bg-primary text-light">
  <div> <small>&copy; Copyright, {{ new Date().getFullYear() }} @Arthiagx.</small></div>
</footer>
</template>

<style>

body{
  height: 100vh 100%;
  background: rgb(157,189,252);
  background: linear-gradient(0deg, rgba(157,189,252,0.5130427170868348) 0%, rgba(0,56,144,0.7063200280112045) 100%);
}

footer{
  position: fixed;
  bottom: 0;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 25px;
}
</style>
