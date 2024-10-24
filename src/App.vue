<template>
  <div class="app-container">
    <header>
      <CxHeader />
    </header>
    <main>
      <CxHero />
    </main>
    <aside>
      <CxDonationForm />
    </aside>
    <footer></footer>
  </div>
</template>

<script>
import CxHeader from "./components/CxHeader.vue";
import CxHero from "./components/CxHero.vue";
import CxDonationForm from "./components/CxDonationForm.vue";

export default {
  name: "App",
  components: {
    CxHeader,
    CxHero,
    CxDonationForm,
  },
};
</script>

<style>
:root {
  --color-black-rgb: 30, 30, 30;
  --color-black: #1e1e1e;
  --color-white: #ffffff;
  --color-lightblue: #00aeef;
  --color-grey: #c1b8b1;
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  
}
body {
  font-family: Arial, sans-serif;
  background-color: var(--color-white);
  color: var(--color-background);
}

aside {
  position: absolute;
  top: 100px;
  left: 80px;
}
</style>
