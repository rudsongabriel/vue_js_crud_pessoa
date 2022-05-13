<template>
  <div id="person-table">
    <Message :msg="msg" v-show="msg" />

    <div>
      <div id="person-table-heading">
        <div class="order-id">#:</div>
        <div>Tipo:</div>
        <div>Nome:</div>
        <div>CPF/CNPJ:</div>
        <div>Foto:</div>
      </div>
    </div>
    <div id="person-table-rows">
      <div class="person-table-row" v-for="person in peoples" :key="person.id">
        <div class="order-number">{{ person.id }}</div>
        <div>{{ person.tipo }}</div>
        <div>{{ person.nome }}</div>
        <div>{{ person.identificacao }}</div>
        <div v-if="person.foto">C/Foto</div>
        <div v-else>S/Foto</div>
        <div>
          <router-link :to="{ name: 'edit', params: { id: person.id } }">
            <button class="click-btn">Editar</button>
          </router-link>
        </div>
        <div>
          <button class="click-btn" @click="deletePerson(person.id)">
            Excluir
          </button>
        </div>
      </div>
    </div>
    <Pagination
      :offset="offset"
      :total="total"
      :limit="limit"
      @change-page="changePage"
    />
  </div>
</template>

<script>
import Message from "./Message.vue";
import Pagination from "./Pagination.vue";
export default {
  name: "Dashboard",
  data() {
    return {
      peoplesbase: null,
      msg: null,
      peoples: [],
      offset: 0,
      limit: 5,
      total: 0,
    };
  },
  components: {
    Message,
    Pagination,
  },
  methods: {
    async getCadastros() {
      const req = await fetch("http://localhost:3000/peoplesbase");
      const data = await req.json();
      this.peoples = [];
      this.peoplesbase = data;
      this.total = data.length;
      let dataIndex = null;
      for (let i = 0; i < this.limit; i++) {
        //console.log(data[i]);
        if (this.offset >= 1) {
          const offsetLimit = this.offset * this.limit;
          dataIndex = offsetLimit + i;
        } else {
          dataIndex = i;
        }
        if (dataIndex < this.total) {
          this.peoples.push(data[dataIndex]);
        }
      }
    },
    async deletePerson(id) {
      const req = await fetch(`http://localhost:3000/peoplesbase/${id}`, {
        method: "DELETE",
      });
      const data = await req.json();

      //colocar uma mensagem de sistema
      this.msg = `Pessoa removida com sucesso!`;
      //limpar mensagem
      setTimeout(() => (this.msg = ""), 3000);

      this.getCadastros();
    },
    changePage(value) {
      this.offset = value;
      this.getCadastros();
    },
  },
  mounted() {
    this.getCadastros();
  },
};
</script>

<style scoped>
#person-table {
  max-width: 1200px;
  margin: 0 auto;
}

#person-table-heading,
#person-table-rows,
.person-table-row {
  display: flex;
  flex-wrap: wrap;
}

#person-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

#person-table-heading div,
.person-table-row div {
  width: 15%;
}

.person-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}

#person-table-heading .order-id,
.person-table-row .order-number {
  width: 5%;
}

select {
  padding: 12px 6px;
  margin-right: 12px;
}

.click-btn {
  background-color: #222;
  color: #ffffff;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.click-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>