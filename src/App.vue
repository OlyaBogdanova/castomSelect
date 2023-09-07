<template>
  <div class="container">
    <h1 class="title">Custom Select: "Hybrid Select"</h1>
    <div class="card">
      <p class="inst">
        Try to select an option with whatever tool you are using (e.g. mouse,
        touch, keyboard, etc...)
      </p>
      <i-select :options="options" label="Main job role" />
    </div>
  </div>
</template>

<script setup>
import iSelect from "@/components/iSelect.vue";
const options = [
  { value: "sel", label: "Select role...", disabled: true },
  { value: "ds", label: "UI/UX Designer", disabled: false },
  { value: "fe", label: "Frontend Engineer", disabled: false },
  { value: "be", label: "Backend Engineer", disabled: false },
  { value: "qa", label: "QA Engineer", disabled: false },
  { value: "un", label: "Unicorn", disabled: false },
];
</script>

<style>
html {
  font-size: 62.5%;
}
body {
  background: #f8f3ef;
  font-family: Arial, Helvetica, sans-serif;
  box-sizing: border-box;
  color: #343434;
  line-height: 1.5;
  font-size: 1.6rem;
  min-height: 120vh; /* using arrow keys in the select, does not scroll the page */
}

body * {
  box-sizing: inherit;
}

strong {
  font-weight: 600;
}
.title {
  font-size: 2rem;
  font-weight: 600;
  margin: 1.6rem;
  line-height: 1.2;
  text-align: center;
}

.card {
  position: relative;
  margin: 2rem auto;
  max-width: calc(100% - 2rem);
  width: 40rem;
  background: white;
  padding: 3rem;
  box-shadow: 0.2rem 0.2rem #e9e1f8;
}

.inst {
  margin-bottom: 1rem;
}
</style>
