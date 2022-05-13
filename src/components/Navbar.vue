<template>
  <div id="nav">
    <nav>
      <router-link to="/">Cadastrar/Editar</router-link>
      <router-link to="/peoples">Listar Cadastros</router-link>
    </nav>
  </div>
</template>
<script>
export default {
  name: "Navbar",
};
</script>
<style scoped>
#nav {
  background-color: #222;
  border-bottom: 4px solid #111;
  padding: 15px 50px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

#nav a {
  color: #eeeeee;
  text-decoration: none;
  margin: 12px;
  transition: 0.5s;
}

#nav a:hover {
  color: #777777;
}
</style>