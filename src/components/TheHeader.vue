<script setup>
import { RouterLink } from 'vue-router'
</script>

<template>
  <header>
    <nav>
      <h1><RouterLink to="/">ONLINE TODO LIST</RouterLink></h1>
      <ul>
        <li class="todo_sm">
          <RouterLink to="/todo"><span>王小明的代辦</span></RouterLink>
        </li>
        <li><a href="#loginPage">登出</a></li>
      </ul>
    </nav>
  </header>
</template>
