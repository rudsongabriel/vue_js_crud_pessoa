<template>
  <div class="main-container">
    <h1>Cadastro/Edição de Pessoas</h1>
    <PersonForm />
  </div>
</template>

<script>
import PersonForm from "../components/PersonForm.vue";

export default {
  name: "Home",
  components: {
    PersonForm,
  },
};
</script>